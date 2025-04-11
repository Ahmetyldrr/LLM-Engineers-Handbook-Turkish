#Ağırlık Çürümesi (Weight Decay) Nedir?
Ağırlık çürümesi, büyük ağırlıklar (weights) için bir ceza (penalty) ekleyerek kayıp fonksiyonuna (loss function) çalışır, modelin daha basit, daha genelleştirilebilir özellikler (features) öğrenmesini teşvik eder. Bu, modelin herhangi bir girdi özelliğine (input feature) aşırı derecede güvenmesini önlemeye yardımcı olur, bu da görünmeyen verilerdeki (unseen data) performansını artırabilir. Tipik olarak, ağırlık çürümesi değerleri (weight decay values) 0.01 ile 0.1 arasında değişir, 0.01 yaygın bir başlangıç noktasıdır (starting point). Örneğin, AdamW optimizasyonunu (optimizer) kullanıyorsanız, ağırlık çürümesini 0.01 olarak ayarlayabilirsiniz. Ağırlık çürümesi faydalı olmakla birlikte, çok yüksek ayarlanması, modelin verilerdeki önemli desenleri (patterns) yakalamasını zorlaştırarak öğrenmeyi engelleyebilir. Tersine, çok düşük ayarlanması yeterli düzenlileştirmeyi (regularization) sağlamayabilir. Optimal ağırlık çürümesi değeri (optimal weight decay value) genellikle spesifik model mimarisine (model architecture) ve veri kümesine (dataset) bağlıdır, bu nedenle farklı değerlerle deney yapmak genellikle iyi bir uygulamadır.

#İngilizce Paragraf
Weight decay works by adding a penalty for large weights to the loss function, encouraging the model to learn simpler, more generalizable features. This helps the model avoid relying too heavily on any single input feature, which can improve its performance on unseen data. Typically, weight decay values range from 0.01 to 0.1, with 0.01 being a common starting point. For example, if you’re using the AdamW optimizer, you might set the weight decay to 0.01. While weight decay can be beneficial, setting it too high can impede learning by making it difficult for the model to capture important patterns in the data. Conversely, setting it too low may not provide sufficient regularization. The optimal weight decay value often depends on the specific model architecture and dataset, so it’s generally a good practice to experiment with different values.

#Kodların Ayrıntılı Açıklaması
Paragrafta kod geçmiyor, ancak ağırlık çürümesi (weight decay) ile ilgili bir örnek kod aşağıda verilmiştir:
```python
# AdamW Optimizasyonu ile Ağırlık Çürümesi
import torch
import torch.nn as nn
import torch.optim as optim

# Model tanımla
model = nn.Linear(5, 3)

# AdamW optimizasyonunu ağırlık çürümesi ile tanımla
optimizer = optim.AdamW(model.parameters(), lr=0.001, weight_decay=0.01)

# Açıklama:
# 1. `model.parameters()`: Modelin parametrelerini (weights ve biases) alır.
# 2. `lr=0.001`: Öğrenme oranı (learning rate) 0.001 olarak ayarlanır.
# 3. `weight_decay=0.01`: Ağırlık çürümesi 0.01 olarak ayarlanır.

# Kaynak kodun her satırının açıklaması:
# 1. `import torch`: PyTorch kütüphanesini içe aktarır.
# 2. `import torch.nn as nn`: PyTorch'un sinir ağları (neural networks) modülünü `nn` takma adı ile içe aktarır.
# 3. `import torch.optim as optim`: PyTorch'un optimizasyon algoritmaları modülünü `optim` takma adı ile içe aktarır.
# 4. `model = nn.Linear(5, 3)`: 5 girişli, 3 çıkışlı doğrusal (linear) bir sinir ağı modeli tanımlar.
# 5. `optimizer = optim.AdamW(model.parameters(), lr=0.001, weight_decay=0.01)`: AdamW optimizasyonunu model parametreleri, öğrenme oranı ve ağırlık çürümesi ile tanımlar.
```

---

