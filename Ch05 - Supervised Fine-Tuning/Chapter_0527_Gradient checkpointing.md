# Gradient Checkpointing (Gradyan Kontrol Noktası)
Gradient checkpointing, ileri geçiş (forward pass) sırasında oluşturulan ara aktivasyonların (intermediate activations) yalnızca bir alt kümesini depolayarak eğitim sırasında bellek tüketimini azaltan bir tekniktir. Standart eğitim prosedürlerinde, tüm ara aktivasyonlar, geri geçiş (backward pass) sırasında gradyan hesaplamasını kolaylaştırmak için bellekte tutulur. Ancak, LLMs gibi çok derin ağlar için, bu yaklaşım, özellikle sınırlı bellek kapasitesine sahip GPU'larda, donanım sınırlamaları nedeniyle hızla pratik olmayabilir. Gradient checkpointing, ağ içindeki belirli katmanlardaki aktivasyonları seçici olarak kaydederek bu zorluğa çözüm getirir. Aktivasyonların kaydedilmediği katmanlar için, gradyan hesaplama için gerektiğinde geri geçiş sırasında yeniden hesaplanır. Bu yaklaşım, hesaplama zamanı ve bellek kullanımı arasında bir denge oluşturur. Bellek gereksinimlerini önemli ölçüde azaltırken, bazı aktivasyonları yeniden hesaplama ihtiyacından dolayı toplam hesaplama süresini artırabilir. Diğer parametreler ve teknikler mevcuttur, ancak daha önce tartışılanlara kıyasla küçük bir rol oynarlar. Bir sonraki bölümde, somut bir örnek kullanarak bu parametrelerin nasıl seçileceğini ve ayarlanacağını inceleyeceğiz.

İngilizce Teknik Terimlerin Türkçeleri ve Açıklamaları:
- Gradient checkpointing: Gradyan kontrol noktası (Bellek tüketimini azaltmak için kullanılan bir teknik)
- Forward pass: İleri geçiş (Ağın girdiden çıktıya doğru işlenmesi)
- Intermediate activations: Ara aktivasyonlar (Ağın ara katmanlarındaki değerler)
- Backward pass: Geri geçiş (Gradyanların hesaplanması için ağın geriye doğru işlenmesi)
- LLMs: Büyük Dil Modelleri (Large Language Models)

Kod bulunmamaktadır, dolayısıyla kod açıklaması da yapılmayacaktır. Paragraf birebir çevrilmiş ve istenen formatta verilmiştir. İngilizce teknik terimler parantez içinde eklenmiştir. Başlık "# Gradient Checkpointing (Gradyan Kontrol Noktası)" şeklinde yazılmıştır.

---

