# Denetimli İnce Ayar (Supervised Fine-Tuning, SFT)
Denetimli İnce Ayar (SFT), Büyük Dil Modellerini (LLM) gerçek dünya uygulamaları için hazırlamada kritik bir adımdır. Başlangıçtaki ön eğitim (pre-training) aşamasından sonra, bir LLM'nin bir dizideki sonraki belirteci (token) tahmin etmeyi öğrenmesiyle, SFT, modelin yeteneklerini özenle seçilmiş talimat çiftleri ve karşılık gelen cevapları kullanarak iyileştirir. Bu süreç iki temel amaca hizmet eder: modele belirli bir sohbet formatını anlamayı ve takip etmeyi öğretir, böylece onu etkili bir şekilde bir konuşma aracına dönüştürür ve modelin geniş bilgi tabanını hedeflenen görevlerde veya uzmanlık alanlarında mükemmelleştirmesini sağlar. SFT'nin önemi, bir modelin genel dil anlayışıyla pratik yararlılığı arasındaki boşluğu doldurma yeteneğinde yatmaktadır. Modele istenen girdi-çıktı desenlerinin örneklerini göstererek, SFT, LLM'nin davranışını belirli hedeflere uydurur, ister görev tamamlama (özetleme veya çeviri gibi) ister alan uzmanlığı (tıbbi veya hukuki bilgi gibi) olsun. Bu özel yaklaşım, yalnızca modelin amaçlanan alanlardaki performansını arttırmakla kalmaz, aynı zamanda talimatları takip etme ve daha ilgili ve tutarlı yanıtlar üretme yeteneğini de geliştirir.

# Bu Bölümde Kapsanan Konular
Bu bölümde aşağıdaki konuları ele alacağız:
- Yüksek kaliteli talimat veri seti oluşturma
- SFT teknikleri
- İnce ayarı uygulama pratiği

Bu bölümün sonunda, kendi talimat veri setlerinizi oluşturabilecek ve LLM'leri bunlarla verimli bir şekilde ince ayar yapabileceksiniz.

# Kod Örnekleri
Bu bölümdeki tüm kod örneklerine GitHub'da https://github.com/PacktPublishing/LLM-Engineering adresinden ulaşabilirsiniz.

### Kod Açıklaması

Bu bölümde verilen kod örnekleri için herhangi bir kod snippet'i bulunmamaktadır. Ancak, varsayımsal bir kod örneği üzerinden gidelim:

```python
# Örnek kod
import pandas as pd

# Veri setini yükleme
data = pd.read_csv("veri_seti.csv")

# Talimatları ve cevapları ayırma
instructions = data["talimat"]
answers = data["cevap"]

# Modeli fine-tuning için hazırlama
# Bu kısımda modelin ve kütüphanelerin yüklenmesi, 
# veri setinin modele uygun formatta hazırlanması gibi işlemler yapılır.

# Modeli fine-tuning yapma
# Bu kısımda, modelin fine-tuning işlemi için gerekli kodlar yer alır.
# Örneğin, Hugging Face Transformers kütüphanesini kullanarak:
from transformers import Trainer, TrainingArguments

training_args = TrainingArguments(
    output_dir="./sonuclar",
    num_train_epochs=3,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=64,
    warmup_steps=500,
    weight_decay=0.01,
    logging_dir="./loglar",
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=dataset,
    eval_dataset=dataset,
)

trainer.train()
```

### Kodun Satır Satır Açıklaması

1. `import pandas as pd`: Pandas kütüphanesini `pd` takma adıyla içe aktarır. Pandas, veri manipülasyonu ve analizi için kullanılan güçlü bir kütüphanedir.

2. `data = pd.read_csv("veri_seti.csv")`: `veri_seti.csv` dosyasını okuyarak bir DataFrame'e yükler. Bu, veri setinin pandas ile işlenmesini sağlar.

3. `instructions = data["talimat"]` ve `answers = data["cevap"]`: DataFrame'den "talimat" ve "cevap" sütunlarını ayırır. Bu, talimatları ve karşılık gelen cevapları ayrı değişkenlerde saklamak için yapılır.

4. `from transformers import Trainer, TrainingArguments`: Hugging Face Transformers kütüphanesinden `Trainer` ve `TrainingArguments` sınıflarını içe aktarır. Bu sınıflar, modelleri eğitmek ve fine-tuning yapmak için kullanılır.

5. `training_args = TrainingArguments(...)`: Eğitim için gerekli argümanları tanımlar. Örneğin, çıktı dizini, eğitim epoch sayısı, batch boyutu gibi hiperparametreler burada belirlenir.

6. `trainer = Trainer(...)`: `Trainer` sınıfının bir örneğini oluşturur. Bu örnek, modeli, eğitim argümanlarını, eğitim ve değerlendirme veri setlerini alır.

7. `trainer.train()`: Modeli, belirtilen argümanlar ve veri seti ile eğitmeye başlar. Bu, fine-tuning işleminin gerçekleştiği adımdır.

Bu kod örneği, bir modelin fine-tuning işleminin nasıl gerçekleştirilebileceğini basitçe göstermektedir. Gerçek uygulamalarda, veri setinin hazırlanması, model seçimi ve hiperparametre optimizasyonu gibi adımlar daha karmaşık olabilir.

---

