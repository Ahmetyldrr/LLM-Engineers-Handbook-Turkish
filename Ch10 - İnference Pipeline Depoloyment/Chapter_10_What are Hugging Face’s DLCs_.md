#DLC (Deep Learning Containers) Nedir?
DLCs, önceden yüklenmiş temel derin öğrenme çerçeveleri (frameworks) ve kütüphaneleri (libraries) içeren özelleştirilmiş Docker imgeleridir (images). Bunlar, Hugging Face'den popüler araçlar (tools) gibi transformers, datasets ve tokenizers içerir. Bu konteynerlar (containers), karmaşık ortam kurulumu (environment setup) ve optimizasyon ihtiyacını ortadan kaldırarak model eğitimi (training) ve dağıtımı (deployment) sürecini basitleştirmek için tasarlanmıştır. Hugging Face Inference DLC, özellikle tamamen entegre bir sunum yığını (serving stack) içerir, dağıtım sürecini önemli ölçüde basitleştirir ve üretimde derin öğrenme modellerini sunmak için gereken teknik uzmanlığı azaltır.

#Model Sunumu (Model Serving)
Model sunumu söz konusu olduğunda, DLC, Hugging Face tarafından yapılan Text Generation Inference (TGI) motoru tarafından desteklenir: https://github.com/huggingface/text-generation-inference. TGI, büyük dil modellerini (LLMs) dağıtmak ve sunmak için açık kaynaklı bir çözümdür (open-source solution). Hugging Face'de bulunan en popüler açık kaynaklı LLMs (örneğin Mistral, Llama ve Falcon) için tensor paralelliği (tensor parallelism) ve dinamik toplu işlem (dynamic batching) kullanarak yüksek performanslı metin üretimi (text generation) sunar.

#DLC'nin Sağladığı Güçlü Özellikler
DLC imajının sağladığı en güçlü özellikler özetle:
- **Tensor Paralelliği (Tensor Parallelism)**: Model çıkarımının (inference) hesaplama verimliliğini artırır.
- **Çıkarım için Optimize Edilmiş Transformers Kodu (Optimized Transformers Code for Inference)**: En yaygın kullanılan mimariler (architectures) genelinde performansı en üst düzeye çıkarmak için flash-attention'ı kullanır: https://github.com/Dao-AILab/flash-attention
- **Bitsandbytes ile Quantization**: Model boyutunu koruyarak performansı korur ve dağıtımları daha verimli hale getirir: https://github.com/bitsandbytes-foundation/bitsandbytes
- **Gelen İsteklerin Sürekli Toplu İşlemesi (Continuous Batching of Incoming Requests)**: İstekleri dinamik olarak toplu işlemek suretiyle verimi artırır.
- **Safetensors Kullanarak Hızlandırılmış Ağırlık Yükleme (Accelerated Weight Loading by Utilizing Safetensors)**: Daha hızlı model başlatma (initialization) için, başlangıç zamanını azaltır: https://github.com/huggingface/safetensors
- **Sunucu-Gönderilen Olaylar (Server-Sent Events - SSE) Yoluyla Gerçek Zamanlı Etkileşimleri Destekleyen Token Akışı (Token Streaming)**: 

#Özet
LLM Twin modelimiz, DLC Docker imgeleri içinde çalışacak, istekleri dinleyecek, çıkarım için LLM'yi optimize edecek ve sonuçları gerçek zamanlı olarak sunacaktır. DLC'nin Docker imgeleri, HTTP istekleri (HTTP requests) yoluyla erişilebilen çıkarım uç noktaları (inference endpoints) altında AWS SageMaker'da barındırılacaktır. Bunu akılda tutarak, uygulamaya geçelim. Önce LLM'yi dağıtacağız ve ardından SageMaker Inference uç noktası ile etkileşimde bulunmak için bir sarmalayıcı sınıfı (wrapper class) yazacağız.

Kod açıklamaları aşağıdaki gibidir:
```markdown
#Yukarıdaki metinde kod bulunmamaktadır.
```
Ancak aşağıdaki gibi bir kod örneği verilebilir:
```python
# Örnek bir Python kodu
import docker

# Docker client oluşturma
client = docker.from_env()

# DLC imajını çekme (pull)
image = client.images.pull('huggingface/text-generation-inference')

# Konteyner oluşturma
container = client.containers.run(image, detach=True, ports={'8000': '8000'})

# Konteyner loglarını yazdırma
print(container.logs())
```
Bu kod, Docker Python kütüphanesini kullanarak DLC imajını çeker ve bir konteyner oluşturur.

1. `import docker`: Docker Python kütüphanesini içe aktarır.
2. `client = docker.from_env()`: Docker client oluşturur.
3. `image = client.images.pull('huggingface/text-generation-inference')`: DLC imajını Docker Hub'dan çeker.
4. `container = client.containers.run(image, detach=True, ports={'8000': '8000'})`: İmajdan bir konteyner oluşturur ve çalıştırır. `-d` bayrağı (`detach=True`) konteynerin arka planda çalışmasını sağlar. `ports={'8000': '8000'}` konteynerin 8000 portunu ana makinenin 8000 portuna bağlar.
5. `print(container.logs())`: Çalışan konteynerin loglarını yazdırır.

---

