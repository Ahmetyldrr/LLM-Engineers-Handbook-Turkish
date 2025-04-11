# Makine Öğrenimi Modellerinin Eğitimi ve Deney Takibi
Makine öğrenimi (ML) modellerinin eğitimi tamamen yinelemeli (iterative) ve deneyseldir (experimental). Geleneksel yazılım geliştirmenin aksine, birden fazla paralel deneyin çalıştırılmasını, önceden tanımlanmış ölçütlere (metrics) göre karşılaştırılmasını ve hangisinin üretime geçeceğine karar verilmesini içerir. Bir deney takip aracı (experiment tracking tool), model tahminlerinizin görsel temsilleri (visual representations) ve ölçütler gibi gerekli tüm bilgilerin kaydedilmesini sağlar, böylece tüm deneylerinizi karşılaştırabilir ve en iyi modeli hızlıca seçebilirsiniz. LLM projemiz de bunun bir istisnası değildir. Şekil 2.11'de gösterildiği gibi, Comet'i kullanarak tüm deneylerimizde eğitim ve değerlendirme kaybı (training and evaluation loss) veya gradyan normunun (gradient norm) değerini izledik.

```
# Şekil 2.11: Comet ML eğitim ölçütleri örneği
```

Deney takip aracı kullanarak, eğitim ve değerlendirme ölçütlerinin ötesine geçebilir ve farklı deneyler arasındaki yapılandırmaları izlemek için eğitim hiperparametrelerinizi (training hyperparameters) kaydedebilirsiniz. Ayrıca, GPU, CPU veya bellek kullanımı (memory utilization) gibi kutudan çıkan sistem ölçütlerini (system metrics) kaydederek, eğitim sırasında hangi kaynaklara ihtiyacınız olduğu ve potansiyel darboğazların (bottlenecks) nerede eğitimizi yavaşlattığı konusunda net bir resim sunar, Şekil 2.12'de gösterildiği gibi.

```
# Şekil 2.12: Comet ML sistem ölçütleri örneği
```

Comet'i yerel olarak kurmanıza gerek yok. Bu kitapta Comet'in çevrimiçi sürümünü ücretsiz ve herhangi bir kısıtlama olmadan kullanacağız. Ayrıca, Comet ML deney takip aracını daha derinlemesine incelemek isterseniz, LLM Twin modellerimizi Comet ML ile izlediğimiz eğitim deneylerini halka açık hale getirdik. Bunlara buradan erişebilirsiniz: https://www.comet.com/mlabonne/llm-twin-training/view/new/panels.

Diğer popüler deney takip araçları (experiment trackers) W&B, MLflow ve Neptune'dur. Hepsiyle çalıştık ve çoğunlukla aynı özelliklere sahip olduklarını söyleyebiliriz, ancak Comet ML kullanım kolaylığı ve sezgisel arayüzü ile kendini ayırt ediyor.

Şimdi, MLOps bulmacasının son parçasına geçelim: Prompt izleme için Opik.

**Kodların Ayrıntılı Açıklaması:**

Yukarıdaki metinde kod bulunmamaktadır. Ancak, deney takip araçları ile ilgili bazı teknik terimlerin açıklamaları aşağıda verilmiştir:

*   `Deney takip aracı (experiment tracking tool)`: Makine öğrenimi deneylerini izlemek ve karşılaştırmak için kullanılan bir araçtır.
*   `Eğitim hiperparametreleri (training hyperparameters)`: Makine öğrenimi modellerinin eğitimi sırasında ayarlanan parametrelerdir.
*   `Gradyan normu (gradient norm)`: Gradyan vektörünün büyüklüğüdür ve modelin eğitimi sırasında kullanılır.
*   `GPU, CPU veya bellek kullanımı (GPU, CPU, or memory utilization)`: Sistem kaynaklarının kullanımını ifade eder.
*   `İnce ayar (fine-tuning)`: Önceden eğitilmiş bir modelin belirli bir görev için yeniden eğitilmesi işlemidir.

İngilizce teknik terimler parantez içinde eklenmiştir. 

Kodların birebir aynısı yazıldı, Türkçeye çevrilmiş metin tamamen aynıdır. 

 Başlıklar # şeklinde yazıldı.

---

