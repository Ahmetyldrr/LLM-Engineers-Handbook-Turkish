# Our Model Can Currently Write Paragraphs About Topics Related to Machine Learning

Our model can currently write paragraphs about topics related to machine learning (Makine Öğrenimi), but it doesn’t have the same writing style as the original authors. This is a typical use case for preference alignment (Tercih Hizalaması), where we want to change the “voice” of the model to closely imitate the source data. It’s important to note that, experimentally, DPO (Direct Preference Optimization - Doğrudan Tercih Optimizasyonu) tends to make models more verbose and pushes them to use very formal language. Therefore, the training will need to use DPO surgically to avoid this pitfall and instead adopt the less formal style of these blog articles. In this section, we will create a preference dataset where the chosen answers are extracts from the text, while rejected answers are generated by the model. To implement it, we will modify the code created in Chapter 5, which was designed to generate instruction datasets (Talimat Veri Kümeleri). As seen in the previous section, preference and instruction datasets rely on the same principles. Instead of pairs of instructions and answers, we need triples (instruction, answer 1, answer 2) (Üçlüler (talimat, cevap 1, cevap 2)). What’s interesting in this setting is that we have ground-truth answers in the text chunks, which means we don’t need complex evaluation processes like LLM judges. To make sure that these extracts are high-quality, we will implement two additional quality filters, based on length and punctuation. Figure 6.2 summarizes the end-to-end process: 
Figure 6.2 – Synthetic data generation pipeline from raw text to preference dataset

We are now ready to implement the preference data generation pipeline: 
We start by importing the necessary libraries.

```python
import concurrent.futures
import json
import re
from typing import List, Tuple
from datasets import Dataset
from openai import OpenAI
from tqdm.auto import tqdm
```

*   Yukarıdaki kodda gerekli kütüphaneler import ediliyor. 
*   `concurrent.futures` kütüphanesi, paralel işlemler yapmak için kullanılıyor.
*   `json` kütüphanesi, JSON formatındaki verileri işlemek için kullanılıyor.
*   `re` kütüphanesi, düzenli ifadelerle çalışmak için kullanılıyor.
*   `List` ve `Tuple` tipleri, type hinting için kullanılıyor.
*   `Dataset` sınıfı, Hugging Face kütüphanesinden import ediliyor ve veri kümeleriyle çalışmak için kullanılıyor.
*   `OpenAI` sınıfı, OpenAI API'sine erişmek için kullanılıyor.
*   `tqdm` fonksiyonu, işlemlerin ilerleme durumunu göstermek için kullanılıyor.

Instead of the `InstructionAnswerSet` class, we now have a `PreferenceSet` class. This class is designed to handle triples of instructions, generated answers (rejected), and extracted answers (chosen).

```python
class PreferenceSet:
    def __init__(self, triples: List[Tuple[str, str, str]]):
        self.triples = triples

    @classmethod
    def from_json(cls, json_str: str) -> 'PreferenceSet':
        data = json.loads(json_str)
        triples = [(triple['instruction'], triple['generated_answer'], triple['extracted_answer']) for triple in data['preference_triples']]
        return cls(triples)

    def __iter__(self):
        return iter(self.triples)
```

*   `PreferenceSet` sınıfı, üçlüleri (talimat, üretilen cevap, çıkarılan cevap) saklamak için kullanılıyor.
*   `__init__` metodu, sınıfın constructor'ıdır ve üçlüleri alır.
*   `from_json` metodu, JSON formatındaki verileri `PreferenceSet` nesnesine çevirmek için kullanılıyor.
*   `__iter__` metodu, sınıfın iterable olmasını sağlar.

The `load_articles_from_json`, `clean_text`, and `extract_substrings` functions remain unchanged from the original code. Let’s start with `load_articles_from_json`, which takes our JSON file (`cleaned_documents.json`) containing the articles as input and returns a Hugging Face dataset with the text and metadata (ID, platform, author ID, author full name, link).

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

*   `load_articles_from_json` fonksiyonu, JSON dosyasından makaleleri yükler ve Hugging Face veri kümesi olarak döndürür.
*   Fonksiyon, JSON dosyasını okur ve `Dataset.from_dict` metodunu kullanarak bir veri kümesi oluşturur.

The `clean_text` function removes non-alphanumeric characters except for apostrophes, periods, commas, exclamation marks, and question marks. It also replaces multiple whitespaces with a single space to ensure proper formatting.

```python
def clean_text(text: str) -> str:
    text = re.sub(r"[^\w\s.,!?']", " ", text)
    text = re.sub(r"\s+", " ", text)
    return text.strip()
```

*   `clean_text` fonksiyonu, metni temizler.
*   Fonksiyon, apostrof, nokta, virgül, ünlem işareti ve soru işareti hariç olmak üzere alfanümerik olmayan karakterleri kaldırır.
*   Fonksiyon, birden fazla whitespace'i tek bir boşlukla değiştirir.

The `extract_substrings` function splits articles into chunks with a length between 1,000 and 2,000 characters. To make sure that the splitting doesn’t break sentences, which could modify their meanings, we use a regex to only split after the end of a sentence.

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

*   `extract_substrings` fonksiyonu, makaleleri belirli bir uzunluk aralığında parçalara ayırır.
*   Fonksiyon, cümleleri bölmemek için düzenli ifade kullanır.

The `generate_preference_triples` function replaces the original `generate_instruction_answer_pairs` function. The prompt is adapted from the instruction version and is designed to generate triples instead of pairs.

```python
def generate_preference_triples(extract: str, client: OpenAI) -> List[Tuple[str, str, str]]:
    prompt = f"""Based on the following extract, generate five instruction-answer triples. Each triple should consist of: 
    1. An instruction asking about a specific topic in the context.
    2. A generated answer that attempts to answer the instruction based on the context.
    3. An extracted answer that is a relevant excerpt directly from the given context.
    Instructions must be self-contained and general, without explicitly mentioning a context, system, course, or extract.
    Important: 
    - Ensure that the extracted answer is a verbatim copy from the context, including all punctuation and apostrophes.
    - Do not add any ellipsis (...) or [...] to indicate skipped text in the extracted answer.
    - If the relevant text is not continuous, use two separate sentences from the context instead of skipping text.
    Provide your response in JSON format with the following structure: 
    {{ "preference_triples": [ {{ "instruction": "...", "generated_answer": "...", "extracted_answer": "..." }}, ... ] }}
    Extract: {extract} """
    completion = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "You are a helpful assistant who generates instruction-answer triples based on the given context. Each triple should include an instruction, a generated answer, and an extracted answer from the context. Provide your response in JSON format."},
            {"role": "user", "content": prompt},
        ],
        response_format={"type": "json_object"},
        max_tokens=2000,
        temperature=0.7,
    )
    result = PreferenceSet.from_json(completion.choices[0].message.content)
    return result.triples
```

*   `generate_preference_triples` fonksiyonu, üçlüler üretir.
*   Fonksiyon, GPT-4o-mini modelini kullanarak talimat, üretilen cevap ve çıkarılan cevaptan oluşan üçlüler üretir.

Two new filtering functions are introduced for the preference data pipeline: `filter_short_answers` and `filter_answer_format`. These functions filter out short answers and ensure that answers start with an uppercase letter and end with proper punctuation.

```python
def filter_short_answers(dataset: Dataset, min_length: int = 100) -> Dataset:
    def is_long_enough(example):
        return len(example['chosen']) >= min_length
    return dataset.filter(is_long_enough)

def filter_answer_format(dataset: Dataset) -> Dataset:
    def is_valid_format(example):
        chosen = example['chosen']
        return (len(chosen) > 0 and chosen[0].isupper() and chosen[-1] in ('.', '!', '?'))
    return dataset.filter(is_valid_format)
```

*   `filter_short_answers` fonksiyonu, kısa cevapları filtreler.
*   `filter_answer_format` fonksiyonu, cevapların formatını kontrol eder.

The `create_preference_dataset` function replaces the original `create_instruction_dataset` function. This function now works with triples instead of pairs and uses different column names in the resulting dataset.

```python
def create_preference_dataset(dataset: Dataset, client: OpenAI, num_workers: int = 4) -> Dataset:
    extracts = extract_substrings(dataset)
    preference_triples = []
    with concurrent.futures.ThreadPoolExecutor(max_workers=num_workers) as executor:
        futures = [
            executor.submit(generate_preference_triples, extract, client) for extract in extracts
        ]
        for future in tqdm(concurrent.futures.as_completed(futures), total=len(futures)):
            preference_triples.extend(future.result())
    instructions, generated_answers, extracted_answers = zip(*preference_triples)
    return Dataset.from_dict(
        {
            "prompt": list(instructions),
            "rejected": list(generated_answers),
            "chosen": list(extracted_answers)
        }
    )
```

*   `create_preference_dataset` fonksiyonu, tercih veri kümesi oluşturur.
*   Fonksiyon, üçlüleri üretir ve bir veri kümesi olarak döndürür.

The `main` function is updated to include the new filtering steps and to use the preference dataset creation function:

```python
def main(dataset_id: str) -> Dataset:
    client = OpenAI()
    # 1. Load the raw data
    raw_dataset = load_articles_from_json("cleaned_documents.json")
    print("Raw dataset:")
    print(raw_dataset.to_pandas())
    # 2. Create preference dataset
    dataset = create_preference_dataset(raw_dataset, client)
    print("Preference dataset:")
    print(dataset.to_pandas())
    # 3. Filter out samples with short answers
    dataset = filter_short_answers(dataset)
    # 4. Filter answers based on format
    dataset = filter_answer_format(dataset)
    # 5. Export
    dataset.push_to_hub(dataset_id)
    return dataset
```

*   `main` fonksiyonu, ana işlemleri gerçekleştirir.
*   Fonksiyon, ham verileri yükler, tercih veri kümesi oluşturur, filtreleme işlemlerini yapar ve veri kümesini dışa aktarır.

---

