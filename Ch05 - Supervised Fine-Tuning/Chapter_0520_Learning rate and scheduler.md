#Öğrenme Oranı ve Öğrenme Oranı Zamanlayıcıları (Learning Rate and Learning Rate Schedulers)

Öğrenme oranı en önemli hiperparametredir (hyperparameter). Modelin parametrelerinin eğitim sırasında ne kadar güncelleneceğini kontrol eder. Genellikle 1e-6 gibi çok küçük değerlerden 1e-3 gibi daha büyük değerlere kadar değişir. Transformatör modelleri (transformer models) için ortak bir başlangıç noktası genellikle 1e-5 civarındadır. Öğrenme oranı çok düşükse, eğitim yavaş ilerler ve suboptimal çözümlerde takılıp kalabilir. Tersine, eğer çok yüksekse, eğitim kararsız hale gelebilir veya sapabilir (diverge), bu da kötü performansa yol açar. Spesifik görev ve modeliniz için en uygun değeri bulmak üzere farklı öğrenme oranlarıyla denemeler yapmak genellikle faydalıdır.

Öğrenme oranı zamanlayıcı (learning rate scheduler), eğitim süreci boyunca öğrenme oranını ayarlar. Genellikle hızlı başlangıç ilerlemesini sağlamak için daha yüksek bir öğrenme oranı ile başlar, daha sonra modeli daha hassas bir şekilde ince ayar yapmak için son aşamalarda kademeli olarak azaltır. En yaygın iki zamanlayıcı türü doğrusal (linear) ve kosinüs (cosine) türleridir. Doğrusal bir zamanlayıcı öğrenme oranını zamanla istikrarlı bir şekilde azaltırken, kosinüs zamanlayıcı bir kosinüs eğrisine (cosine curve) uymaktadır, ilk başta daha yavaş ve eğitim sonuna doğru daha hızlı azalmaktadır. Örneğin, 3e-4 öğrenme oranı ile başlayabilir ve eğitim boyunca 1e-7'ye kadar azaltabilirsiniz. Spesifik değerler ve azalım çizelgesi (decay schedule) modelinize ve veri setinize bağlıdır, ancak ortak bir yaklaşım, öğrenme oranının 0'dan başlangıç değerine yükseldiği bir ısınma periyodu (warmup period) (örneğin, toplam adımların %5'i) kullanmaktır, ardından kalan adımların %95'i için bir azalım periyodu (decay period) kullanmaktır. Bu yaklaşım, erken eğitimi stabilize etmeye yardımcı olur ve model yakınsarken (converge) daha rafine güncellemeler sağlar. Genel olarak, doğrusal ve kosinüs zamanlayıcılar aynı performans seviyesini sağlar.

**Kod:**
```python
# Örnek kod
import torch
from torch.optim import Adam
from torch.optim.lr_scheduler import CosineAnnealingLR

# Model ve optimizer tanımlama
model = ...  # Model tanımı
optimizer = Adam(model.parameters(), lr=3e-4)

# Kosinüs zamanlayıcı tanımlama
scheduler = CosineAnnealingLR(optimizer, T_max=100, eta_min=1e-7)

# Eğitim döngüsü
for epoch in range(100):
    # Eğitim adımları...
    optimizer.step()
    scheduler.step()
```

**Kod Açıklaması:**

1. `import torch`: PyTorch kütüphanesini içe aktarır.
2. `from torch.optim import Adam`: Adam optimizerını PyTorch kütüphanesinden içe aktarır.
3. `from torch.optim.lr_scheduler import CosineAnnealingLR`: Kosinüs zamanlayıcısını PyTorch kütüphanesinden içe aktarır.
4. `model = ...`: Model tanımı yapılır (örneğin, transformatör modeli).
5. `optimizer = Adam(model.parameters(), lr=3e-4)`: Adam optimizerı tanımlanır ve model parametreleri ile 3e-4 öğrenme oranı ile başlatılır.
6. `scheduler = CosineAnnealingLR(optimizer, T_max=100, eta_min=1e-7)`: Kosinüs zamanlayıcısı tanımlanır. `T_max` parametresi, öğrenme oranının azalım periyodunu belirler (örneğin, 100 epoch). `eta_min` parametresi, öğrenme oranının minimum değerini belirler (örneğin, 1e-7).
7. `for epoch in range(100)`: Eğitim döngüsü başlar.
8. `# Eğitim adımları...`: Eğitim adımları gerçekleştirilir (örneğin, veri yükleme, ileri ve geri yayılım).
9. `optimizer.step()`: Optimizer adımını gerçekleştirir (model parametrelerini günceller).
10. `scheduler.step()`: Zamanlayıcı adımını gerçekleştirir (öğrenme oranını günceller).

Bu kod, kosinüs zamanlayıcısı kullanarak öğrenme oranını ayarlayan bir örnek eğitim döngüsü göstermektedir.

---

