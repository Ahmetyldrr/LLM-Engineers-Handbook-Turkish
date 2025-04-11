# Supervised Fine-Tuning (SFT) ve Preference Alignment

Denetimli İnce Ayar (Supervised Fine-Tuning, SFT) büyük dil modellerini (LLMs) belirli görevleri gerçekleştirmek üzere uyarlamada çok önemli olmuştur. Ancak, SFT, insan tercihlerini ve bir modelin karşılaşabileceği potansiyel etkileşimlerin uzun kuyruğunu (long tail) yakalamakta zorlanır. Bu sınırlama, yapay zeka sistemlerini insan tercihleriyle uyumlu hale getirmek için preference alignment terimi altında toplanan daha gelişmiş tekniklerin geliştirilmesine yol açmıştır. Preference alignment, doğrudan insan veya yapay zeka geri bildirimini (feedback) eğitim sürecine dahil ederek SFT'nin eksikliklerini giderir. Bu yöntem, basit denetimli öğrenmenin (supervised learning) yetersiz kaldığı karmaşık senaryolarda insan tercihlerini daha iyi anlamayı sağlar. Preference alignment için çok sayıda teknik mevcut olmakla birlikte, bu bölüm basitlik ve verimlilik açısından esas olarak Doğrudan Tercih Optimizasyonu'na (Direct Preference Optimization, DPO) odaklanacaktır.

# Bu Bölümde Neler Öğreneceğiz?

Bu bölümde, DPO gibi preference alignment algoritmalarının gerektirdiği veri türü hakkında konuşacağız. Modelimizin yazı stilini daha az yapay ve daha otantik hale getirmek için kendi veri setimizi oluşturacağız. DPO algoritmasını tanıtacağız ve 5. Bölüm'de eğitilen modeli uyumlu hale getirmek için uygulayacağız. Bu bölümde aşağıdaki konuları ele alacağız:
- Tercih veri setlerini (preference datasets) anlamak
- Kendi tercih veri setimizi nasıl oluştururuz?
- Doğrudan Tercih Optimizasyonu (Direct Preference Optimization, DPO)
- Modelimizi uyumlu hale getirmek için DPO'yu pratikte uygulamak

Bu bölümün sonunda, kendi tercih veri setlerinizi oluşturabilecek ve çeşitli tekniklerle modelleri uyumlu hale getirebileceksiniz. Bu bölümdeki tüm kod örnekleri GitHub'da https://github.com/PacktPublishing/LLM-Engineering adresinde bulunabilir.

### Kod Açıklaması

Bu bölümde verilen kod örnekleri için herhangi bir kod pasajı verilmemiştir. Ancak, varsayalım ki bir kod pasajı verildi ve bizden bunu ayrıntılı olarak açıklaymamız istendi.

Örnek Kod:
```python
# Kod örneği yok, varsayalım ki bir kod pasajı verildi
import pandas as pd

# Veri setini yükleme
data = pd.read_csv("veri_seti.csv")

# Veri setini işleme
processed_data = data.apply(lambda x: x.strip())

# Modeli eğitme
model.fit(processed_data)
```

1. `import pandas as pd`: Bu satır, pandas kütüphanesini `pd` takma adı ile içe aktarır. Pandas, veri işleme ve analizinde kullanılan güçlü bir Python kütüphanesidir.
2. `data = pd.read_csv("veri_seti.csv")`: Bu satır, "veri_seti.csv" dosyasını pandas DataFrame'e yükler. Bu, CSV formatındaki veri setlerini okumak için kullanılır.
3. `processed_data = data.apply(lambda x: x.strip())`: Bu satır, veri setindeki her bir elemana `strip()` fonksiyonunu uygular. `strip()`, bir stringin başındaki ve sonundaki boşlukları temizler. Burada, lambda fonksiyonu her bir elemanı temizlemek için kullanılır.
4. `model.fit(processed_data)`: Bu satır, işlenmiş veri setini kullanarak modeli eğitir. `fit()` metodu, makine öğrenimi modellerinde yaygın olarak kullanılan bir yöntemdir ve modeli verilen verilere uyumlu hale getirir.

Bu şekilde, her bir kod satırı açıklanarak, okuyucunun kodu anlaması ve uygulaması kolaylaştırılır.

---

