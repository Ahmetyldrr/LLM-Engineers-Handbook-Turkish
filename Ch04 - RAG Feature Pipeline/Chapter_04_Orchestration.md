#ZenML ile Toplu RAG Özellik Ardışık Düzeni (Batch RAG Feature Pipeline)

ZenML, toplu RAG özellik ardışık düzenini (batch RAG feature pipeline) düzenleyecektir (orchestrate). ZenML kullanarak, bunu bir zamanlamaya göre (schedule) çalıştırmak üzere planlayabiliriz, örneğin her saat başı, veya hızlı bir şekilde manuel olarak tetikleyebiliriz (trigger). Diğer bir seçenek ise ETL veri toplama ardışık düzeni (ETL data collection pipeline) tamamlandıktan sonra tetiklemektir. Özellik ardışık düzenini düzenleyerek (orchestrating the feature pipeline) ve ZenML (veya diğer düzenleme araçlarına) entegre ederek, özellik ardışık düzenini sürekli eğitim (Continuous Training, CT) nihai amacı ile operasyon haline getirebiliriz. Düzenleme (orchestration), zamanlama (scheduling) ve sürekli eğitim (Continuous Training, CT) tüm detaylarına 11. Bölüm'de gireceğiz.

İngilizce teknik terimlerin Türkçe karşılıkları parantez içinde eklenmiştir:
- RAG: (Belirli bir bağlamda kullanılan bir kısaltma, ancak burada açıkça tanımlanmamış)
- ETL: (Extract, Transform, Load - Veri toplama ve işleme sürecini ifade eder)
- CT: (Continuous Training - Sürekli Eğitim)

Kod bulunmamaktadır, ancak metinde geçen "pipeline" terimi genellikle aşağıdaki gibi kodlarla temsil edilir:
```python
# Örnek bir ardışık düzen (pipeline) kodu
from zenml import pipeline

@pipeline
def my_pipeline():
    # Ardışık düzen adımları burada tanımlanır
    pass
```
Yukarıdaki kod örneği ayrıntılı olarak açıklanmıştır:
1. `from zenml import pipeline`: ZenML kütüphanesinden `pipeline` dekoratörünü içe aktarır. Bu, bir ardışık düzen tanımlamak için kullanılır.
2. `@pipeline`: Bu dekoratör, bir ardışık düzen tanımladığımızı belirtir.
3. `def my_pipeline():`: Ardışık düzenin adını tanımlar. Bu örnekte `my_pipeline` olarak adlandırılmıştır.
4. `# Ardışık düzen adımları burada tanımlanır`: Bu kısım, ardışık düzenin gerçek adımlarının tanımlanacağı yerdir. Örneğin, veri yükleme, veri temizleme, model eğitimi gibi adımlar burada yer alabilir.
5. `pass`: Python'da bir bloğu boş bırakmak için kullanılan bir deyimdir. Burada ardışık düzenin gövdesi boş olduğu için `pass` ifadesi kullanılmıştır. Gerçek bir uygulamada, ardışık düzenin adımları burada tanımlanacaktır.

---

