# Cevapları Değerlendirme (Evaluating Answers)

Cevaplarımızı değerlendirmek için GPT-4o-mini'yi (GPT-4o-mini) bir yargıç olarak kullanacağız. Bu strateji, veri üretimi için kullandığımıza benzer. Aslında, veri üretim süreci sırasında kötü örnekleri filtrelemek için uyarlayabilirsiniz. Burada, her modelden üretilen her cevabı doğruluk (accuracy) ve stil (style) açısından puanlayacağız. Ortalama puanlar, Llama-3.1-8B-Instruct ile karşılaştırıldığında ince ayarımızın (fine-tuning) kalitesi hakkında bilgi verecektir.

İlk olarak, openai dahil gerekli kütüphaneleri içe aktarıyoruz (importing libraries):

```python
import json
from typing import List
from datasets import Dataset, load_dataset
from openai import OpenAI
from tqdm.auto import tqdm
import concurrent.futures
```

Ardından, `evaluate_answer()` fonksiyonunu tanımlarız. Bu fonksiyon, doğruluk ve stil temelinde cevapları değerlendirmek için değerlendirme istemimizi (evaluation prompt) içerir:

```python
def evaluate_answer(instruction: str, answer: str, client: OpenAI) -> dict:
    prompt = f"""You are an expert judge. Please evaluate the quality of a given answer to an instruction based on two criteria:
    1. Accuracy: How factually correct is the information presented in the answer? You are a technical expert in this topic.
    2. Style: Is the tone and writing style appropriate for a blog post or social media content? It should use simple but technical words and avoid formal or academic language.

    Accuracy scale:
    1 (Poor): Contains factual errors or misleading information
    2 (Good): Mostly accurate with minor errors or omissions
    3 (Excellent): Highly accurate and comprehensive

    Style scale:
    1 (Poor): Too formal, uses some overly complex words
    2 (Good): Good balance of technical content and accessibility, but still uses formal words and expressions
    3 (Excellent): Perfectly accessible language for blog/social media, uses simple but precise technical terms when necessary

    Instruction: {instruction}
    Answer: {answer}

    Provide your evaluation in JSON format with the following structure:
    {{ "accuracy" : {{ "analysis" : "..." , "score" : 0 }}, "style" : {{ "analysis" : "..." , "score" : 0 }}
    }} """
```

Bu istem, GPT-4o-mini modeline bir kullanıcı sorgusu olarak verilir. Sistem istemi (system prompt), cevapları doğruluk ve stil temelinde değerlendirmek istediğimizi vurgular:

```python
completion = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "You are a helpful assistant who evaluates answers based on accuracy and style. Provide your response in JSON format with a short analysis and score for each criterion."},
        {"role": "user", "content": prompt},
    ],
    response_format={"type": "json_object"},
    max_tokens=1000,
    temperature=0.8,
)
```

Önceki bölümlerde olduğu gibi, süreci hızlandırmak için isteklerimizi toplu olarak göndereceğiz (batching requests). Bu nedenle, `evaluate_batch()` fonksiyonunu oluştururuz, bu fonksiyon ayrıştırılmış yapılandırılmış çıktıları (parsed structured outputs) ilgili endekslerle (indices) birlikte döndürür:

```python
def evaluate_batch(batch, start_index):
    client = OpenAI(api_key=OPENAI_KEY)
    return [(i, evaluate_answer(instr, ans, client)) for i, (instr, ans) in enumerate(batch, start=start_index)]
```

Şimdi, önceki kodu `evaluate_answers()` fonksiyonunda düzenleyebiliriz. Bu fonksiyon, model kimliği (model ID), iş parçacığı sayısı (number of threads) ve toplu iş boyutu (batch size) girdilerini alır:

```python
def evaluate_answers(model_id: str, num_threads: int = 10, batch_size: int = 5) -> Dataset:
    dataset = load_dataset(f"mlabonne/{model_id.split('/')[-1]}-results", split="all")
```

Veri setimizden talimat-cevap çiftlerini (instruction-answer pairs) toplu olarak oluştururuz:

```python
batches = [(i, list(zip(dataset["instruction"][i:i+batch_size], dataset["answers"][i:i+batch_size]))) for i in range(0, len(dataset), batch_size)]
```

Paralel değerlendirme (parallel evaluation) için birden fazla iş parçacığı kullanırız:

```python
evaluations = [None] * len(dataset)
with concurrent.futures.ThreadPoolExecutor(max_workers=num_threads) as executor:
    futures = [executor.submit(evaluate_batch, batch, start_index) for start_index, batch in batches]
    for future in tqdm(concurrent.futures.as_completed(futures), total=len(futures)):
        for index, evaluation in future.result():
            evaluations[index] = evaluation
```

Değerlendirme sonuçlarını içeren yeni bir sütun (column) oluştururuz:

```python
if 'evaluation' in dataset.column_names:
    dataset = dataset.remove_columns(['evaluation'])
dataset = dataset.add_column("evaluation", evaluations)
```

JSON çıktısını ayrıştırarak (parsing JSON output) doğruluk ve stil puanlarını elde ederiz:

```python
accuracy_scores = []
style_scores = []
for evaluation in dataset['evaluation']:
    try:
        eval_dict = json.loads(evaluation) if isinstance(evaluation, str) else evaluation
        accuracy_score = eval_dict['accuracy']['score']
        style_score = eval_dict['style']['score']
        accuracy_scores.append(accuracy_score)
        style_scores.append(style_score)
    except (json.JSONDecodeError, KeyError, TypeError):
        accuracy_scores.append(None)
        style_scores.append(None)
```

Doğruluk ve stil puanları için iki yeni sütun ekleriz:

```python
if 'accuracy' in dataset.column_names:
    dataset = dataset.remove_columns(['accuracy'])
dataset = dataset.add_column('accuracy', accuracy_scores)
if 'style' in dataset.column_names:
    dataset = dataset.remove_columns(['style'])
dataset = dataset.add_column('style', style_scores)
```

Son olarak, oluşturulan cevaplar, değerlendirmeler ve puanlar içeren veri setini Hugging Face Hub'a göndeririz (pushing to Hugging Face Hub):

```python
dataset.push_to_hub(f"mlabonne/{model_id.split('/')[-1]}-results")
return dataset
```

Seçtiğimiz üç model için `evaluate_answers()` fonksiyonunu çağırabiliriz:

```python
model_ids = ['mlabonne/TwinLlama-3.1-8B', 'mlabonne/TwinLlama-3.1-8B-DPO', 'meta-llama/Meta-Llama-3.1-8B-Instruct']
for model_id in model_ids:
    evaluate_answers(model_id)
```

Ara sonuçları kaydederek, değerlendirme çerçevemizi daha sağlam hale getiririz ve diğer modellere, veri setlerine ve ölçütlere kolayca genişletilebilir.

### Kod Açıklaması

1. `evaluate_answer()` fonksiyonu, bir talimat ve cevabı değerlendirir ve doğruluk ile stil puanları döndürür.
   - `instruction`: Değerlendirilecek talimat.
   - `answer`: Değerlendirilecek cevap.
   - `client`: OpenAI istemcisi.

2. `evaluate_batch()` fonksiyonu, bir toplu işi değerlendirir ve sonuçları döndürür.
   - `batch`: Değerlendirilecek talimat-cevap çiftlerinin listesi.
   - `start_index`: Başlangıç endeksi.

3. `evaluate_answers()` fonksiyonu, bir modelin cevaplarını değerlendirir ve sonuçları döndürür.
   - `model_id`: Değerlendirilecek modelin kimliği.
   - `num_threads`: Kullanılacak iş parçacığı sayısı.
   - `batch_size`: Toplu iş boyutu.

4. Kod, değerlendirme sonuçlarını içeren yeni sütunlar oluşturur ve JSON çıktısını ayrıştırarak doğruluk ve stil puanlarını elde eder.

5. Son olarak, oluşturulan cevaplar, değerlendirmeler ve puanlar içeren veri setini Hugging Face Hub'a gönderir.

---

