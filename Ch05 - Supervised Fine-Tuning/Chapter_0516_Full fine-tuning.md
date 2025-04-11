# Full Fine-Tuning (Tam İnce Ayarlama)

Tam ince ayar (full fine-tuning), temel modeldeki her bir parametrenin yeniden eğitilmesinden oluşan en basit SFT (Supervised Fine-Tuning) tekniğini ifade eder. Ön eğitim (pre-training) gibi, SFT de eğitim amacı olarak sonraki-token tahmini (next-token prediction) kullanır. Bu, daha önce tartışılan veri kümesi yapısının, sürekli ön eğitim (continual pre-training) ve tam ince ayar arasındaki ana fark olarak görülebileceği anlamına gelir. Bu yöntem genellikle en iyi sonuçları sağlar, ancak önemli hesaplama kaynakları gerektirir.

Bellek kullanımı, model boyutu, eğitim teknikleri ve optimizasyon yöntemleri dahil olmak üzere çeşitli faktörlere bağlıdır. En basit haliyle, tek-GPU (single-GPU) ayarı kullanıldığında, gerekli bellek aşağıdaki formül kullanılarak tahmin edilebilir:

Parametreler (Parameters): Bir sinir ağındaki öğrenilebilir ağırlıklar ve biaslardır. Büyük bir dil modelinde, bunlar tipik olarak dikkat mekanizmaları (attention mechanisms), ileri beslemeli katmanlar (feed-forward layers) ve embedding katmanlarındaki ağırlıklardır. 
Maliyet (Cost): 4 byte/parametre (FP32) veya 2 byte/parametre (FP16/BF16).

Gradyanlar (Gradients): Girdiler, kaybın her bir model parametresi ile ilgili kısmi türevleridir. Her bir parametrenin kaybı minimize etmek için ne kadar ayarlanması gerektiğini gösterir. Eğitim sırasında, gradyanlar her bir parametre için geri yayılım (backpropagation) yoluyla hesaplanır ve model parametrelerini güncellemek için kullanılır. 
Maliyet (Cost): 4 byte/parametre.

Optimize Edici Durumları (Optimizer States): Optimize edici durumları, Adam veya AdamW gibi optimizasyon algoritmaları tarafından tutulan ek değerlerdir. Bunlar tipik olarak her bir parametre için geçmiş gradyanların ve geçmiş kare gradyanların hareketli ortalamalarını içerir. Her bir parametre için öğrenme oranını uyarlamaya ve kayıp manzarasını daha etkili bir şekilde gezmeye yardımcı olurlar. Örneğin, Adam her bir parametre için iki ek değer (momentum ve varyans) tutar. 
Maliyet (Cost): 8 byte/parametre (Adam optimize edicisi için).

Aktivasyonlar (Activations): Aktivasyonlar, ileri geçiş (forward pass) sırasında sinir ağının her bir katmanının ara çıktılarıdır. Transformer tabanlı modeller için, bu, dikkat mekanizmalarının, ileri beslemeli katmanların ve normalizasyon katmanlarının çıktılarını içerir. Aktivasyonlar, aktivasyon kontrol noktası (activation checkpointing) gibi teknikler kullanılmadıkça, geri geçişte gradyanları hesaplamak için bellekte tutulmaları gerekir. 
Maliyet (Cost): değişken, ancak küçük batch boyutları için genellikle ihmal edilebilir.

Bu bize parametre başına 16 byte'lık bir temel değer verir. Bu, 7B model için 112 GB VRAM ve 70B model için 1,120 GB'ye çevrilir. Ancak, bu genellikle bir hafife alma olarak görülür, çünkü aktivasyonlar, geçici tamponlar ve çeşitli eğitim tekniklerinden kaynaklanan ek bellek ihtiyacını hesaba katmaz.

LLM ince ayarını sırasında bellek kullanımını azaltmak için çeşitli teknikler kullanılabilir. Model paralelliği (model parallelism), iş yükünü birden fazla GPU'ya dağıtır, ancak bazı ek yükler ekler. Gradyan birikimi (gradient accumulation), orantılı bellek artışı olmadan daha büyük efektif batch boyutları sağlar. 8-bit Adam gibi bellek verimli optimize ediciler, optimize edici durumlarının ayak izini azaltabilir. Aktivasyon kontrol noktası (activation checkpointing), belirli aktivasyonları yeniden hesaplayarak hesaplama için bellek takas eder. Bu teknikler birleştirildiğinde, bellek kullanımını önemli ölçüde azaltabilir. Örneğin, model paralelliği ile karışık hassasiyet kullanmak, maliyetleri 16 byte'lık temel değerine kıyasla parametre başına 14-15 byte civarına düşürebilir. Ancak, büyük modeller için bellek gereksinimleri bu optimizasyonlarla bile önemli olmaya devam eder.

Ayrıca, tam ince ayar, ön eğitim ağırlıklarını doğrudan değiştirir, bu da doğası gereği yıkıcı olmasını sağlar. Eğitim beklendiği gibi davranmazsa, önceki bilgiyi ve becerileri silebilir - bu olgu "felaketli unutma" (catastrophic forgetting) olarak adlandırılır. Aynı olgu, sürekli ön eğitim ile de gerçekleşebilir, bu da genellikle bu teknikleri kullanmayı zorlaştırır. Bu ek karmaşıklık ve yüksek hesaplama gereksinimleri nedeniyle, parametre-verimli teknikler genellikle görev ve alan-özel modeller oluşturmak için tam ince ayara tercih edilir.

**Kod Açıklaması**

Yukarıdaki metinde herhangi bir kod bulunmamaktadır. Ancak, bellek kullanımını tahmin etmek için kullanılan formül ve bazı teknik terimler açıklanmıştır.

Formül:
```
Bellek kullanımı = (Parametreler x 4 byte/parametre) + (Gradyanlar x 4 byte/parametre) + (Optimize Edici Durumları x 8 byte/parametre) + Aktivasyonlar
```
Bu formül, FP32 hassasiyeti için parametre başına 16 byte'lık bir temel değer verir.

**Teknik Terimlerin Açıklaması**

*   **FP32**: 32-bit kayan nokta hassasiyeti
*   **FP16/BF16**: 16-bit kayan nokta hassasiyeti
*   **Adam**: Bir optimizasyon algoritması
*   **AdamW**: Adam'ın bir varyantı
*   **Model Paralelliği**: İş yükünü birden fazla GPU'ya dağıtma tekniği
*   **Gradyan Birikimi**: Orantılı bellek artışı olmadan daha büyük efektif batch boyutları sağlama tekniği
*   **Aktivasyon Kontrol Noktası**: Belirli aktivasyonları yeniden hesaplayarak hesaplama için bellek takas etme tekniği

---

