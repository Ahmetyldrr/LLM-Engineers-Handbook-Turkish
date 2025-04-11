#Batching ve Continuous Batching (Sürekli Toplu İşleme)

Batching, or processing multiple inference requests simultaneously, is a standard approach to achieve high throughput. 
 Toplu işleme (Batching), birden fazla çıkarım (inference) isteğini aynı anda işleme, yüksek verimlilik elde etmek için standart bir yaklaşımdır.

Larger batch sizes spread out the memory cost of model weights and transfer more data to the GPU at once, better saturating its parallel compute capacity. 
Daha büyük toplu iş boyutları, model ağırlıklarının (model weights) bellek maliyetini dağıtır ve daha fazla veriyi aynı anda GPU'ya aktararak paralel hesaplama kapasitesini daha iyi doyurur.

However, decoder-only models pose a particular challenge due to the high variability in input prompt lengths and desired output lengths. 
Ancak, sadece çözücü (decoder-only) modelleri, girdi istemi (input prompt) uzunlukları ve istenen çıktı uzunluklarındaki yüksek değişkenlik nedeniyle özel bir zorluk teşkil eder.

Some requests may have short prompts and only need a one-word answer, while others may input a lengthy context and expect a multi-paragraph response. 
Bazı istekler kısa istemlere sahip olabilir ve yalnızca tek kelimelik bir cevap gerektirebilirken, diğerleri uzun bir içerik girebilir ve çok-paragraf bir yanıt bekleyebilir.

With traditional batching, we would have to wait for the longest request in a batch to complete before starting a new batch. 
Geleneksel toplu işlemede (traditional batching), yeni bir toplu işleme başlamadan önce bir toplu işlemdeki en uzun isteğin tamamlanmasını beklemek zorunda kalırız.

This leads to under-utilization as the accelerator sits partly idle waiting for a straggling request to finish. 
Bu, hızlandırıcı (accelerator) kısmen boşta beklerken birtakım isteklerin bitmesini beklemek zorunda kaldığı için düşük kullanım oranına yol açar.

#Continuous Batching (Sürekli Toplu İşleme)

Continuous batching , also known as in-flight batching, aims to prevent idle time by immediately feeding a new request into the batch as soon as one completes. 
Sürekli toplu işleme (Continuous batching), aynı zamanda uçuş içi toplu işleme (in-flight batching) olarak da bilinir, bir istek tamamlanır tamamlanmaz yeni bir isteği toplu işleme hemen dahil ederek boşta bekleme süresini önlemeyi amaçlar.

The batching process begins the same – by filling the batch with initial requests. 
Toplu işleme süreci aynı şekilde başlar - ilk isteklerle toplu işlemeyi doldurarak.

But as soon as a request completes its generation, it is evicted from the batch and a new request takes its place. 
Ancak bir istek üretimini tamamladığında, toplu işlemden çıkarılır ve yerine yeni bir istek dahil edilir.

This way, the accelerator is always processing a full batch, leading to maximally efficient hardware utilization. 
Bu şekilde, hızlandırıcı her zaman tam bir toplu işleme işlemektedir, bu da donanımın maksimum verimlilikle kullanılmasını sağlar.

An additional consideration is the need to periodically pause the generation process to run prefill, or the embedding and encoding of waiting requests. 
Ek bir husus, bekleyen isteklerin gömme (embedding) ve kodlamasını (encoding) çalıştırmak için üretim sürecini periyodik olarak duraklatma ihtiyacıdır.

Finding the optimal balance between generation and prefill requires some tuning of the waiting-served ratio hyperparameter. 
Üretim ve ön doldurma (prefill) arasındaki optimal dengeyi bulmak, bekleyen-serv edilen oranı hiperparametresinin (waiting-served ratio hyperparameter) bazı ayarlamalar yapılmasını gerektirir.

Continuous batching is natively implemented in most inference frameworks, like Hugging Face’s Text Generation Inference ( TGI ), vLLM, and NVIDIA TensorRT-LLM. 
Sürekli toplu işleme, Hugging Face'in Metin Üretimi Çıkarımı (Text Generation Inference - TGI), vLLM ve NVIDIA TensorRT-LLM gibi çoğu çıkarım çerçevesinde (inference framework) yerel olarak uygulanmaktadır.

**Kod Açıklaması:**

Yukarıdaki metinde kod bulunmamaktadır. Ancak, sürekli toplu işleme (Continuous Batching) kavramının uygulanması genellikle aşağıdaki adımları içerir:

1. İlk isteklerle toplu işleme doldurulur.
2. Bir istek üretimini tamamladığında, toplu işlemden çıkarılır ve yerine yeni bir istek dahil edilir.
3. Üretim süreci periyodik olarak duraklatılır ve bekleyen isteklerin gömme ve kodlaması yapılır.

Bu adımların uygulanması genellikle bir programlama dilinde (örneğin Python) yazılmış bir kod ile gerçekleştirilir. Aşağıda örnek bir Python kodu verilmiştir:
```python
import numpy as np

class ContinuousBatching:
    def __init__(self, batch_size, max_requests):
        self.batch_size = batch_size
        self.max_requests = max_requests
        self.requests = []
        self.generating = []

    def add_request(self, request):
        self.requests.append(request)

    def generate(self):
        # Üretim süreci
        for i in range(self.batch_size):
            if self.generating:
                # Üretim tamamlandı
                self.generating.pop(0)
            if self.requests:
                # Yeni istek dahil edilir
                self.generating.append(self.requests.pop(0))

    def prefill(self):
        # Bekleyen isteklerin gömme ve kodlaması
        for request in self.requests:
            # Gömme ve kodlama işlemi
            pass

# Kullanım örneği
batching = ContinuousBatching(batch_size=32, max_requests=100)
batching.add_request("İstek 1")
batching.add_request("İstek 2")
batching.generate()
batching.prefill()
```
Bu kod, sürekli toplu işleme kavramının basit bir örneğini göstermektedir. Gerçek dünya uygulamalarında, daha karmaşık ve optimize edilmiş kodlar kullanılmaktadır.

---

