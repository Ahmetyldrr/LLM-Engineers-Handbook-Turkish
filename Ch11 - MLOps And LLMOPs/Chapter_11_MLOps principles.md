# MLOps Alanını Yönlendiren Altı Temel İlkeler

MLOps alanını yönlendiren altı temel ilke vardır. Bunlar, herhangi bir araçtan bağımsızdır ve sağlam ve ölçeklenebilir makine öğrenimi (ML) sistemleri oluşturmanın temelini oluşturur. Bu ilkeler şunlardır:

Otomasyon veya Operasyonelleştirme (Automation or Operationalization): MLOps'teki otomasyon, manuel süreçlerden otomatik ardışık düzenlere (pipelines) CT ve CI/CD aracılığıyla geçişi içerir. Bu, yeni veriler, performans düşüşleri veya işlenmeyen uç durumlar (edge cases) gibi tetikleyicilere yanıt olarak makine öğrenimi modellerinin verimli bir şekilde yeniden eğitilmesini ve dağıtılmasını sağlar. Manuel deneylerden tam otomasyona geçiş, makine öğrenimi sistemlerimizin sağlam, ölçeklenebilir ve değişen gereksinimlere hatasız ve gecikmesiz olarak uyarlanabilir olmasını sağlar.

```markdown
### Otomasyon veya Operasyonelleştirme 
- Manuel süreçlerden otomatik ardışık düzenlere geçiş 
- CT ve CI/CD kullanılarak yapılır 
- Yeni veriler, performans düşüşleri veya işlenmeyen uç durumlar gibi tetikleyicilere yanıt verir
```

Sürümleme (Versioning): MLOps'te, kod, modeller ve verilerdeki değişiklikleri bireysel olarak izlemek, tutarlılık ve yeniden üretilebilirlik sağlamak çok önemlidir. Kod, Git gibi araçlar kullanılarak izlenir, modeller model kayıtları (model registries) aracılığıyla sürümlenir ve veri sürümleme, DVC veya artifact yönetim sistemleri gibi çözümler kullanılarak yönetilir.

```markdown
### Sürümleme 
- Kod, modeller ve verilerdeki değişiklikleri izler 
- Kod için Git, modeller için model kayıtları, veri için DVC veya artifact yönetim sistemleri kullanılır
```

Deney Takibi (Experiment Tracking): Makine öğrenimi modellerinin eğitimi, önceden tanımlanmış metrikler temelinde birden fazla deneyin karşılaştırılmasını içeren yinelemeli ve deneysel bir süreç olduğundan, en iyi modeli seçmemize yardımcı olmak için bir deney takip aracı kullanmak önemlidir. Comet ML, W&B, MLflow ve Neptune gibi araçlar, deneyleri kolayca karşılaştırmak ve üretim için en iyi modeli seçmek için gerekli tüm bilgileri kaydetmemize olanak tanır.

```markdown
### Deney Takibi 
- Deneyleri karşılaştırmak için kullanılır 
- Comet ML, W&B, MLflow, Neptune gibi araçlar kullanılır
```

Test Etme (Testing): MLOps, kodunuzu test etmenin yanı sıra, verilerinizi ve modellerinizi de birim, entegrasyon, kabul, regresyon ve stres testleri aracılığıyla test etmenizi önerir. Bu, her bileşenin doğru çalışmasını ve iyi entegre olmasını sağlar, girdilere, çıktılara ve uç durumların işlenmesine odaklanır.

```markdown
### Test Etme 
- Kod, veri ve modelleri test eder 
- Birim, entegrasyon, kabul, regresyon ve stres testleri kullanılır
```

İzleme (Monitoring): Bu aşama, üretim verilerindeki değişiklikler nedeniyle servis edilen makine öğrenimi modellerindeki performans düşüşlerini tespit etmek için hayati önem taşır, böylece zamanında müdahale (retraining, prompt veya feature engineering, veya veri doğrulama gibi) yapılabilir. Günlükleri, sistem metriklerini ve model metriklerini izleyerek ve driftleri tespit ederek, makine öğrenimi sistemlerinin sağlığını üretimde koruyabilir, sorunları mümkün olduğunca hızlı bir şekilde tespit edebilir ve doğru sonuçlar vermeye devam etmelerini sağlayabiliriz.

```markdown
### İzleme 
- Performans düşüşlerini tespit eder 
- Günlükler, sistem metrikleri ve model metrikleri izlenir, driftler tespit edilir
```

Yeniden Üretilebilirlik (Reproducibility): Bu, makine öğrenimi sistemlerinizdeki her sürecin (örneğin eğitim veya feature engineering) aynı girdi verildiğinde aynı sonuçları üretmesini sağlar, kod sürümleri, veri sürümleri, hiperparametreler veya diğer konfigürasyon türleri gibi tüm değişkenleri izleyerek. Makine öğrenimi eğitimi ve çıkarımının deterministik olmayan doğası nedeniyle, pseudo-random sayılar üretirken iyi bilinen tohumlar (seeds) ayarlamak, tutarlı sonuçlar elde etmek ve süreçleri mümkün olduğunca deterministik hale getirmek için önemlidir.

```markdown
### Yeniden Üretilebilirlik 
- Aynı girdi için aynı sonuçları sağlar 
- Kod sürümleri, veri sürümleri, hiperparametreler ve diğer konfigürasyonlar izlenir
- Deterministik olmayan doğa nedeniyle tohumlar (seeds) ayarlamak önemlidir
```

İngilizce teknik terimlerin Türkçeleri parantez içinde verilmiştir:
- Automation or Operationalization: Otomasyon veya Operasyonelleştirme
- Versioning: Sürümleme
- Experiment Tracking: Deney Takibi
- Testing: Test Etme
- Monitoring: İzleme
- Reproducibility: Yeniden Üretilebilirlik
- CT: Continuous Training (Sürekli Eğitim)
- CI/CD: Continuous Integration/Continuous Deployment (Sürekli Entegrasyon/Sürekli Dağıtım)
- Edge cases: Uç Durumlar
- Model registries: Model Kayıtları
- Artifact management systems: Artifact Yönetim Sistemleri
- Hyperparameters: Hiperparametreler
- Seeds: Tohumlar (Rasgele sayı üretiminde kullanılan başlangıç değerleri)

---

