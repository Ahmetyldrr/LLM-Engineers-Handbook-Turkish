# Yapay Öğretim Veri Kümesi Oluşturma (Synthetic Data Generation)

Bu bölümde, 3. Bölüm'den elde edilen taranmış verilere dayanarak kendi talimat (instruction) veri kümemizi oluşturacağız. Yüksek kaliteli bir talimat veri kümesi oluşturmak için iki ana sorunu ele almamız gerekiyor: verilerimizin yapılandırılmamış doğası (unstructured nature) ve tarayabileceğimiz makale sayısının sınırlı olması. Bu yapılandırılmamış doğa, talimat ve cevap çiftleri yerine ham metinlerle (makaleler) uğraşmamızdan kaynaklanmaktadır. Bu sorunu ele almak için, bu dönüşümü gerçekleştirmek üzere bir LLM (Large Language Model) kullanacağız. Özellikle, geri çeviri (backtranslation) ve yeniden ifade etme (rephrasing) kombinasyonunu kullanacağız. Geri çeviri, beklenen cevabı çıktı olarak vermeyi ve buna karşılık gelen talimatı oluşturmayı ifade eder. Ancak, bir paragraf gibi bir metin parçasını cevap olarak kullanmak her zaman uygun olmayabilir. Bu nedenle, ham metni yeniden ifade ederek düzgün biçimlendirilmiş, yüksek kaliteli cevaplar üreteceğimizden emin olmak istiyoruz. Ayrıca, orijinal paragrafa yakın kalmak için yazarın yazı stilini takip etmesini modelden isteyebiliriz. Bu süreç kapsamlı bir istem mühendisliği (prompt engineering) içerse de, otomatize edilebilir ve aşağıdaki uygulamada görüleceği gibi ölçeklendirilebilir.

İkinci sorunumuz olan sınırlı örnek sayısı, gerçek dünya kullanım durumlarında oldukça yaygındır. Alabileceğimiz makale sayısı sınırlıdır, bu da oluşturabileceğimiz talimat veri kümesinin boyutunu kısıtlar. Bu örnekte, daha fazla örneğe sahip olduğumuzda, model orijinal yazarları taklit etmede daha iyi hale gelir. Bu sorunu ele almak için, makalelerimizi parçalara ayıracağız ve her bir parça için üç talimat-cevap çifti oluşturacağız. Bu, oluşturduğumuz örnek sayısını çeşitliliği koruyarak artıracaktır. Basitlik adına, OpenAI'ın GPT-4o-mini modelini kullanacağız, ancak açık kaynaklı modelleri de kullanabilirsiniz.

LLM'ler yapılandırılmış çıktı üretmede güvenilir değildir. Belirli şablonlar veya talimatlar verildiğinde bile, modelin bunlara tutarlı bir şekilde uyması garanti değildir. Bu tutarsızlık, çıktının istenen biçimi karşıladığından emin olmak için genellikle ek dize ayrıştırması (string parsing) gerektirir. Bu süreci basitleştirmek ve düzgün yapılandırılmış sonuçlar sağlamak için yapılandırılmış oluşturma (structured generation) tekniklerini kullanabiliriz. Yapılandırılmış oluşturma, bir LLM'yi JSON, pydantic sınıfları veya normal ifadeler gibi önceden tanımlanmış bir şablonu takip etmeye zorlamak için etkili bir yöntemdir. Aşağıda, OpenAI'ın JSON modu özelliğini kullanacağız, bu özellik daha sağlam bir şekilde geçerli JSON nesneleri döndürür ve kapsamlı son işlemeye olan ihtiyacı azaltır.

Bu açıklamaya dayanarak, aşağıdaki şekil oluşturmak istediğimiz yapay veri ardışık düzeni (synthetic data pipeline) her adımını özetlemektedir.

## Kodlar ve Açıklamaları

### Gerekli Kütüphanelerin Yüklenmesi
```python
import concurrent.futures
import json
import random
import re
from concurrent.futures import ThreadPoolExecutor
from typing import List, Tuple
from datasets import Dataset
from openai import OpenAI
from pydantic import BaseModel, Field
from tqdm.auto import tqdm
```
Bu kod, gerekli kütüphaneleri yükler. `concurrent.futures` eşzamanlı işlemler için, `json` JSON verileriyle çalışmak için, `re` düzenli ifadelerle çalışmak için, `datasets` Hugging Face veri kümeleriyle çalışmak için, `openai` OpenAI API'siyle etkileşim için, `pydantic` veri doğrulama ve ayrıştırma için, ve `tqdm` ilerleme çubuğu göstermek için kullanılır.

### Makalelerin JSON Dosyasından Yüklenmesi
```python
def load_articles_from_json(file_path: str) -> Dataset:
    with open(file_path, "r") as file:
        data = json.load(file)
    return Dataset.from_dict(
        {
            "id": [item["id"] for item in data["artifact_data"]],
            "content": [item["content"] for item in data["artifact_data"]],
            "platform": [item["platform"] for item in data["artifact_data"]],
            "author_id": [item["author_id"] for item in data["artifact_data"]],
            "author_full_name": [item["author_full_name"] for item in data["artifact_data"]],
            "link": [item["link"] for item in data["artifact_data"]],
        }
    )
```
Bu fonksiyon, belirtilen JSON dosyasından makaleleri yükler ve bir Hugging Face veri kümesi olarak döndürür.

### Metin Temizleme
```python
def clean_text(text):
    text = re.sub(r"[^\w\s.,!?']", " ", text)
    text = re.sub(r"\s+", " ", text)
    return text.strip()
```
Bu fonksiyon, metni temizler. İlk olarak, harf, rakam, boşluk, noktalama işaretleri dışında kalan karakterleri boşlukla değiştirir. Ardından, birden fazla ardışık boşluğu tek boşlukla değiştirir ve metnin başındaki ve sonundaki boşlukları kaldırır.

### Metin Parçalama
```python
def extract_substrings(dataset: Dataset, min_length: int = 1000, max_length: int = 2000) -> List[str]:
    extracts = []
    sentence_pattern = r"(?<!\w\.\w.)(?<![A-Z][a-z]\.)(?<=\.|\?|\!)\s"
    for article in dataset["content"]:
        cleaned_article = clean_text(article)
        sentences = re.split(sentence_pattern, cleaned_article)
        current_chunk = ""
        for sentence in sentences:
            sentence = sentence.strip()
            if not sentence:
                continue
            if len(current_chunk) + len(sentence) <= max_length:
                current_chunk += sentence + " "
            else:
                if len(current_chunk) >= min_length:
                    extracts.append(current_chunk.strip())
                current_chunk = sentence + " "
        if len(current_chunk) >= min_length:
            extracts.append(current_chunk.strip())
    return extracts
```
Bu fonksiyon, makaleleri belirli bir uzunluk aralığında parçalara ayırır.

### Talimat-Cevap Çiftleri Oluşturma
```python
def generate_instruction_answer_pairs(extract: str, client: OpenAI) -> List[Tuple[str, str]]:
    prompt = f"""Based on the following extract, generate five instruction-answer pairs. Each instruction must ask to write about a specific topic contained in the context. Each answer must provide a relevant paragraph based on the information found in the context. Only use concepts from the context to generate the instructions. Instructions must never explicitly mention a context, a system, a course, or an extract. Instructions must be self-contained and general. Answers must imitate the writing style of the context. Example instruction: Explain the concept of an LLM Twin. Example answer: An LLM Twin is essentially an AI character that mimics your writing style, personality, and voice. It's designed to write just like you by incorporating these elements into a language model. The idea is to create a digital replica of your writing habits using advanced AI techniques. Provide your response in JSON format with the following structure: {{ "instruction_answer_pairs": [ {{"instruction": "...", "answer": "..."}}, ... ] }} Extract: {extract} """
    completion = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "You are a helpful assistant who generates instruction-answer pairs based on the given context. Provide your response in JSON format."},
            {"role": "user", "content": prompt},
        ],
        response_format={"type": "json_object"},
        max_tokens=1200,
        temperature=0.7,
    )
    result = InstructionAnswerSet.from_json(completion.choices[0].message.content)
    return result.pairs
```
Bu fonksiyon, verilen bir metin parçası için talimat-cevap çiftleri oluşturur.

### Talimat Veri Kümesi Oluşturma
```python
def create_instruction_dataset(dataset: Dataset, client: OpenAI, num_workers: int = 4) -> Dataset:
    extracts = extract_substrings(dataset)
    instruction_answer_pairs = []
    with concurrent.futures.ThreadPoolExecutor(max_workers=num_workers) as executor:
        futures = [executor.submit(generate_instruction_answer_pairs, extract, client) for extract in extracts]
        for future in tqdm(concurrent.futures.as_completed(futures), total=len(futures)):
            instruction_answer_pairs.extend(future.result())
    instructions, answers = zip(*instruction_answer_pairs)
    return Dataset.from_dict({"instruction": list(instructions), "output": list(answers)})
```
Bu fonksiyon, makalelerden talimat-cevap çiftleri oluşturarak bir talimat veri kümesi oluşturur.

### Ana Fonksiyon
```python
def main(dataset_id: str) -> Dataset:
    client = OpenAI()
    raw_dataset = load_articles_from_json("cleaned_documents.json")
    instruction_dataset = create_instruction_dataset(raw_dataset, client)
    filtered_dataset = instruction_dataset.train_test_split(test_size=0.1)
    filtered_dataset.push_to_hub("mlabonne/llmtwin")
    return filtered_dataset
```
Bu fonksiyon, ham verileri yükler, talimat veri kümesi oluşturur, eğitim ve test kümelerine ayırır ve Hugging Face Hub'a yükler.

# İngilizce Teknik Terimlerin Türkçeleri Parantez İçinde
- unstructured nature (yapılandırılmamış doğa)
- backtranslation (geri çeviri)
- rephrasing (yeniden ifade etme)
- prompt engineering (istem mühendisliği)
- structured generation (yapılandırılmış oluşturma)
- string parsing (dize ayrıştırma)
- LLM (Large Language Model - Büyük Dil Modeli)

Tüm kodlar ve açıklamaları yukarıda verilmiştir.

---

