#Model Paralelliği (Model Parallelism)

Model paralelliği, büyük dil modellerinin (LLMs) bellek ve hesaplama gereksinimlerini birden fazla GPU'ya dağıtmanıza olanak tanır. Bu, tek bir cihazda sığmayacak kadar büyük modellerin eğitilmesini ve çıkarımını (inference) mümkün kılar, ayrıca performansı (tokens per second) açısından da iyileştirir. Model paralelliğine üç ana yaklaşım vardır, her biri model ağırlıklarını (model weights) ve hesaplamayı farklı şekillerde bölmeyi içerir: veri paralelliği (data parallelism), ardışık düzen paralelliği (pipeline parallelism) ve tensör paralelliği (tensor parallelism). Bu yaklaşımlar başlangıçta eğitim için geliştirilmiş olsa da, yalnızca ileri geçişe (forward pass) odaklanarak bunları çıkarım için yeniden kullanabiliriz.

İngilizce paragraf:
```
Model parallelism allows you to distribute the memory and compute requirements of LLMs across multiple GPUs. This enables the training and inference of models too large to fit on a single device, while also improving performance in terms of throughput (tokens per second). There are three main approaches to model parallelism, each involving splitting the model weights and computation in different ways: data parallelism , pipeline parallelism , and tensor parallelism . Although these approaches were originally developed for training, we can reuse them for inference by focusing on the forward pass only.
```

Kodları ayrıntılı olarak açıklayacak bir kod bulunmamaktadır. Ancak, eğer model paralelliği ile ilgili bir kod örneği verilseydi, bunu satır satır açıklayabilirdik.

Örneğin, PyTorch'ta veri paralelliği (data parallelism) için kullanılan `DataParallel` modülünün kullanımını gösteren bir kod:
```python
import torch
import torch.nn as nn
import torch.distributed as dist

# Model tanımlama
class MyModel(nn.Module):
    def __init__(self):
        super(MyModel, self).__init__()
        self.fc1 = nn.Linear(5, 10)
        self.fc2 = nn.Linear(10, 5)

    def forward(self, x):
        x = torch.relu(self.fc1(x))
        x = self.fc2(x)
        return x

# Modeli oluşturma
model = MyModel()

# Veri paralelliği için modelin paralel hale getirilmesi
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
model = nn.DataParallel(model, device_ids=[0, 1])  # 0 ve 1. GPU'ları kullan

# Modeli eğitme
input_data = torch.randn(10, 5).to(device)
output = model(input_data)
```
Bu kodda:
- `MyModel` sınıfı, basit bir sinir ağı modelini tanımlar.
- `DataParallel` modülü, modeli birden fazla GPU'da paralel olarak çalıştırmak için kullanılır.
- `device_ids` parametresi, hangi GPU'ların kullanılacağını belirtir.
- `model(input_data)` çağrısı, modeli paralel olarak çalıştırarak çıktıyı hesaplar.

Bu kod örneği, model paralelliğinin nasıl uygulanabileceğini gösteren basit bir örnektir. Gerçek dünya uygulamalarında, daha karmaşık modeller ve paralel işlemler kullanılabilir.

---

