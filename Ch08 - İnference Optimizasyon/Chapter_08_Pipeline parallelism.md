# Pipeline Parallelism (PP)

Huang ve diğerleri tarafından GPipe makalesinde (2019) tanıtılan pipeline parallelism (PP), büyük sinir ağlarının eğitimi ve çalıştırılmasının hesaplama yükünü birden fazla GPU'ya dağıtmak için bir stratejidir. Geleneksel DP'nin (Data Parallelism) aksine, her GPU'da tüm modeli kopyalamak yerine, pipeline parallelism modelin katmanlarını farklı GPU'lara böler. Bu yaklaşım, her GPU'nun modelin belirli bir bölümünü işlemesine olanak tanır, böylece bireysel GPU'ların bellek yükünü azaltır.

# Şekil 8.5 – Dört GPU ile pipeline parallelism örneği

Şekil 8.5'te gösterildiği gibi, tipik bir dört yönlü pipeline parallel bölünmesinde, model dört bölüme ayrılır ve her bölüm farklı bir GPU'ya atanır. Modelin ilk %25'i GPU 1 tarafından, sonraki %25'i GPU 2 tarafından işlenebilir ve bu şekilde devam eder. İleri geçiş (forward pass) sırasında, aktivasyonlar hesaplanır ve sonra bir sonraki GPU'ya iletilir. Eğitim için, geri geçiş (backward pass) benzer bir sırayı tersine takip eder, gradyanlar GPU'lar aracılığıyla geriye doğru yayılır. GPU sayısına genellikle parallelism derecesi denir.

Pipeline parallelism'in birincil avantajı, GPU başına bellek gereksinimlerini önemli ölçüde azaltma yeteneğidir. Ancak, bu yaklaşım, özellikle pipeline'ın sıralı doğası ile ilgili yeni zorluklar ortaya koyar. Başlıca sorunlardan biri, "pipeline baloncukları" (pipeline bubbles) oluşmasıdır. Bu baloncuklar, bazı GPU'ların boşta kaldığında, önceki katmanlardan aktivasyonları beklerken ortaya çıkar. Bu boş zaman, sürecin genel verimliliğini azaltabilir.

# Micro-batching

Micro-batching, pipeline baloncuklarının etkisini azaltmak için geliştirilmiştir. Giriş grubunu daha küçük alt gruplara bölerek, micro-batching GPU'ların daha yoğun çalışmasını sağlar, çünkü bir sonraki alt grup, önceki işlem tamamlanmadan önce işlenmeye başlanabilir.

# Şekil 8.6 – Micro-batching ile pipeline parallelism örneği

Şekil 8.6, micro-batching ile pipeline parallelism örneğini gösterir. Bu örnekte, pipeline dört aşamaya sahiptir (F0, F1, F2, F3) ve giriş grubu dört micro-batch'e bölünmüştür. GPU 0, sırasıyla F0,0, F0,1, F0,2 ve F0,3 ileri yollarını işler. F0,0 tamamlandığında, GPU 1 hemen F1,0'ı işlemeye başlayabilir ve bu şekilde devam eder. Bu ileri geçişler tamamlandıktan sonra, GPU 0, diğer GPU'ların sırasıyla ileri hesaplamalarını bitirmelerini bekler, daha sonra geri yollarını (B0,3, B0,2, B0,1 ve B0,0) başlatır.

Pipeline parallelism, Megatron-LM, DeepSpeed (ZeRO) ve PyTorch için özel Pipeline Parallelism (PiPPy) kitaplığı aracılığıyla dağıtık eğitim çerçevelerinde uygulanır. Yazının yazıldığı sırada, yalnızca TensorRT-LLM gibi bazı çıkarım (inference) çerçeveleri pipeline parallelism'i desteklemektedir.

**Kodların ayrıntılı açıklaması:**

Bu metinde, kodlar yerine teknik terimler ve kavramlar açıklanmaktadır. Ancak, pipeline parallelism ve micro-batching kavramlarını açıklayan bir örnek kod parçası aşağıdaki gibi olabilir:

```python
# Örnek kod parçası
import torch
import torch.distributed as dist
import torch.nn as nn

# Modeli tanımla
class Model(nn.Module):
    def __init__(self):
        super(Model, self).__init__()
        self.fc1 = nn.Linear(5, 10)
        self.fc2 = nn.Linear(10, 5)

    def forward(self, x):
        x = torch.relu(self.fc1(x))
        x = self.fc2(x)
        return x

# Pipeline parallelism için modeli böl
class PipelineModel(nn.Module):
    def __init__(self):
        super(PipelineModel, self).__init__()
        self.fc1 = nn.Linear(5, 10)
        self.fc2 = nn.Linear(10, 5)

    def forward(self, x):
        # İleri geçiş
        x = torch.relu(self.fc1(x))
        # Micro-batching için x'i bir sonraki aşamaya gönder
        return x

# Micro-batching için giriş grubunu böl
def micro_batching(input_batch, num_micro_batches):
    micro_batches = []
    batch_size = input_batch.size(0)
    for i in range(num_micro_batches):
        start = i * batch_size // num_micro_batches
        end = (i + 1) * batch_size // num_micro_batches
        micro_batches.append(input_batch[start:end])
    return micro_batches

# Pipeline parallelism ve micro-batching ile eğitim
def train(model, input_batch, num_micro_batches):
    micro_batches = micro_batching(input_batch, num_micro_batches)
    outputs = []
    for micro_batch in micro_batches:
        output = model(micro_batch)
        outputs.append(output)
    return outputs

# Modeli ve pipeline parallelism'i başlat
model = PipelineModel()
input_batch = torch.randn(16, 5)
num_micro_batches = 4
outputs = train(model, input_batch, num_micro_batches)

# İngilizce teknik terimleri parantez içinde ekledim
# pipeline parallelism (PP)
# Data Parallelism (DP)
# forward pass (İleri geçiş)
# backward pass (Geri geçiş)
# micro-batching (Micro-batching)
```

Bu örnek kod parçası, pipeline parallelism ve micro-batching kavramlarını göstermektedir. Gerçek dünya uygulamalarında, bu kavramlar daha karmaşık modeller ve dağıtık eğitim çerçeveleri kullanılarak uygulanır.

---

