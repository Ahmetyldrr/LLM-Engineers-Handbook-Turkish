# İlk Adım: Yanıtların Üretilmesi
İlk adım, test setimizdeki her bir talimat (instruction) için yanıtların verimli bir şekilde üretilmesinden oluşur. İki modelimize ek olarak, yaptığımız trade-off'ları (trade-offs) daha iyi anlamak için bir referans noktası olarak Llama-3.1-8B'nin resmi talimat (instruct) versiyonu olan meta-llama/Meta-Llama-3.1-8B-Instruct'u da kullanacağız. Uygulamanın ilk aşamasını başlatalım: İlgili kütüphaneleri (libraries) içe aktarıyoruz, bunlara hızlı üretme (fast generation) için vLLM de dahildir. Bu kütüphane, yerel modellerle (local models) toplu üretme (batch generation) için transformers'dan çok daha hızlıdır:

```python
from vllm import LLM, SamplingParams
from datasets import load_dataset
from tqdm.auto import tqdm
import gc
```

`generate_answers` adlı bir fonksiyon tanımlıyoruz, bu fonksiyon veri setimizi (dataset) işleyecek ve belirtilen bir model kullanarak yanıtlar üretecektir. Bu fonksiyon iki girdi alır - kullanmak istediğimiz modelin kimliği (ID) ve test veri setinin adı:

```python
def generate_answers(model_id, dataset_name):
    dataset = load_dataset(dataset_name, split="test")
```

Ham talimatları (raw instructions), modellerimizin eğitildiği sohbet şablonunu (chat template) kullanarak biçimlendirmemiz (format) gerekir. Llama-3.1-8B-Instruct'un farklı bir şablonla kullanıldığını, ancak bu basit biçimi takip edebileceğini unutmayın. Burada basitlik adına her modelle aynı sohbet şablonunu kullanıyoruz. Tüm test setini bu şablona `format()` fonksiyonuyla eşliyoruz (map):

```python
def format(sample):
    return "Below is an instruction that describes a task. Write a response that appropriately completes the request.\n\n### Instruction:\n{}\n\n### Response:\n".format(sample["instruction"])

dataset = dataset.map(lambda sample: {"prompt": format(sample)})
```

vLLM tarafından kullanılan LLM nesnesini (LLM object) maksimum 4.096 belirteç (token) uzunluğuyla başlatıyoruz. Örnekleme parametrelerini (sampling parameters) de belirtebiliriz, bunlar kod çözme stratejisinde (decoding strategy) kullanılan değişkenlere karşılık gelir. Burada çeşitliliği teşvik eden parametreler (yüksek sıcaklık - high temperature) kullanırken en düşük olasılıklı belirteçleri (tokens) kaldırıyoruz (`top_p` ve `min_p`). Son olarak, `dataset["prompt"]` ile istemlerin (prompts) listesini vererek üretimi başlatıyoruz:

```python
llm = LLM(model=model_id, max_model_len=4096)
sampling_params = SamplingParams(temperature=0.8, top_p=0.95, min_p=0.05, max_tokens=4096)
outputs = llm.generate(dataset["prompt"], sampling_params)
```

Bu işlem, 334 istemimizle birkaç dakika sürmelidir. Bu bittiğinde, vLLM tarafından çıktı alınan nesneden (output object) yanıtları çıkarıyoruz. Daha sonra bu yanıtları veri setimize yeni bir sütun olarak ekliyoruz. Bu, yanıtları kaydetmek ve daha sonra incelemek için yararlıdır:

```python
answers = [output.outputs[0].text for output in outputs]
dataset = dataset.add_column("answers", answers)
```

Sonuçlarımızı daha sonra kolay erişim için Hugging Face Hub'a yüklüyoruz. Ardından, bir sonraki modeli işlediğimizde alanın dolmasını önlemek için GPU belleğimizi temizliyoruz:

```python
print(f"Uploading results for {model_id}")
dataset.push_to_hub(f"mlabonne/{model_id.split('/')[-1]}-results")
gc.collect()
return dataset
```

Test etmek istediğimiz üç modelin listesini oluşturuyoruz. Ardından, bu modellerin her biri için `generate_answers()` fonksiyonunu sırasıyla çalıştırıyoruz. Bu, her model için ayrı bir sonuç seti oluşturacak ve yükleyecektir:

```python
model_ids = ['mlabonne/TwinLlama-3.1-8B', 'mlabonne/TwinLlama-3.1-8B-DPO', 'meta-llama/Meta-Llama-3.1-8B-Instruct']
for model_id in model_ids:
    generate_answers(model_id, "mlabonne/llmtwin")
```

Artık yanıt üretimini tamamladığımıza göre, değerlendirme (evaluation) sürecine geçebiliriz.

### Kodların Ayrıntılı Açıklaması

1. **`from vllm import LLM, SamplingParams`**: vLLM kütüphanesinden `LLM` sınıfını ve `SamplingParams` sınıfını içe aktarır. `LLM` büyük dil modellerini çalıştırmak için, `SamplingParams` ise örnekleme parametrelerini tanımlamak için kullanılır.

2. **`dataset = load_dataset(dataset_name, split="test")`**: Belirtilen `dataset_name` adındaki veri setini yükler ve "test" bölümünü `dataset` değişkenine atar.

3. **`def format(sample):`**: Ham talimatları biçimlendirmek için kullanılan bir fonksiyon tanımlar. Bu, sohbet şablonuna göre talimatları yeniden biçimlendirir.

4. **`llm = LLM(model=model_id, max_model_len=4096)`**: `model_id` ile belirtilen modeli, maksimum 4096 belirteç uzunluğuyla başlatır.

5. **`sampling_params = SamplingParams(temperature=0.8, top_p=0.95, min_p=0.05, max_tokens=4096)`**: Örnekleme parametrelerini tanımlar. `temperature` çeşitliliği, `top_p` ve `min_p` en düşük olasılıklı belirteçleri kontrol eder, `max_tokens` maksimum çıktı uzunluğunu belirler.

6. **`outputs = llm.generate(dataset["prompt"], sampling_params)`**: `dataset["prompt"]` içindeki istemleri kullanarak, belirtilen örnekleme parametreleriyle metin üretimini gerçekleştirir.

7. **`answers = [output.outputs[0].text for output in outputs]`**: Üretilen çıktıdan metinleri çıkararak `answers` listesine atar.

8. **`dataset = dataset.add_column("answers", answers)`**: `answers` listesini `dataset` veri setine "answers" adlı yeni bir sütun olarak ekler.

9. **`dataset.push_to_hub(f"mlabonne/{model_id.split('/')[-1]}-results")`**: Sonuçları Hugging Face Hub'a belirtilen isimle yükler.

10. **`gc.collect()`**: GPU belleğini temizler.

11. **`for model_id in model_ids:`**: Belirtilen model kimlikleri listesinde döngü yaparak her model için `generate_answers` fonksiyonunu çalıştırır.

---

