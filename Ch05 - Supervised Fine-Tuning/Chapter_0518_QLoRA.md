#QLoRA: Bellek Kullanımını Azaltarak Büyük Dil Modellerini İnce Ayarlama Yöntemi

Dettmers ve ekibi tarafından tanıtılan QLoRA, yüksek hesaplama maliyetlerinin zorluklarını ele alan büyük dil modellerini (LLMs) ince ayarlamak için bir yöntemdir. Quantization tekniklerini LoRA ile birleştirerek, QLoRA geliştiricilerin nispeten küçük, yaygın olarak bulunan GPU'larda modelleri ince ayarlamasına olanak tanır. QLoRA'nın yaklaşımının özü, temel model parametrelerini özel bir 4-bit NormalFloat (NF4) veri türüne quantize etmeyi içerir, bu da bellek kullanımını önemli ölçüde azaltır. LoRA gibi, ince ayar sırasında tüm model parametrelerini güncellemek yerine, QLoRA küçük, eğitilebilir düşük rank matrisleri (adaptörler) modelin belirli katmanlarına tanıtır. Sadece bu adaptörler eğitim sırasında güncellenir, orijinal model ağırlıkları değişmeden kalır. Bellek kullanımını daha da azaltmak için, QLoRA çift quantization kullanır, bu da quantization sabitlerini quantize eder. Ayrıca, Nvidia'nın unified memory özelliğinden yararlanarak eğitim sırasında bellek patlamalarını yönetmek için paged optimizers kullanır.

QLoRA, LoRA'ya kıyasla önemli ölçüde bellek tasarrufu sağlar, tepe GPU bellek kullanımını %75'e kadar azaltır. Örneğin, 7B modeli için QLoRA, başlatma sırasında tepe bellek kullanımını 14 GB'den 9.1 GB'ye düşürür, bu %35'lik bir azalmadır. İnce ayar sırasında, bellek tasarrufu %40'a çıkar, LoRA için 15.6 GB'den QLoRA için 9.3 GB'ye. Ancak, bu bellek verimliliği, QLoRA'nın LoRA'dan yaklaşık %30 daha yavaş olması pahasına gelir. Model performansı açısından, QLoRA LoRA'ya kıyasla sadece küçük farklılıklar gösterir. Özetle, QLoRA özellikle çok büyük modellerle veya sınırlı GPU belleği olan donanımlarda çalışırken bellek kısıtlamaları öncelikli bir endişe olduğunda faydalıdır. Ancak, eğitim hızı kritik olduğunda ve yeterli bellek mevcut olduğunda, LoRA tercih edilen seçim olabilir. QLoRA ve LoRA arasındaki karar, projenin özel gereksinimlerine, mevcut donanıma ve bellek kullanımı, eğitim hızı ve model performansı arasında denge kurma ihtiyacına bağlı olmalıdır.

İngilizce teknik terimlerin Türkçe karşılıkları parantez içinde eklenmiştir:
- Quantization (Nicemleme)
- LoRA (Düşük Rank Adaptasyonu)
- LLMs (Büyük Dil Modelleri)
- NF4 (4-bit NormalFloat veri türü)
- Adapters (Adaptörler)
- Paged optimizers (Sayfalandırılmış optimizasyon algoritmaları)
- Unified memory (Birleşik bellek)

Kodların ayrıntılı açıklaması:
Bu metinde kod bulunmamaktadır, ancak QLoRA'nın çalışma prensibi ve kullandığı teknikler açıklanmıştır.

- Quantization (Nicemleme): Model parametrelerini daha düşük hassasiyetli bir veri türüne dönüştürme işlemi.
- LoRA (Düşük Rank Adaptasyonu): Modelin belirli katmanlarına düşük rank matrisleri ekleyerek ince ayar yapma yöntemi.
- QLoRA'nın quantization işleminde NF4 veri türünü kullanması, bellek kullanımını azaltır.
- Adaptörlerin kullanılması, sadece bu adaptörlerin eğitim sırasında güncellenmesini sağlar, orijinal model ağırlıkları değişmez.
- Çift quantization, quantization sabitlerini quantize ederek bellek kullanımını daha da azaltır.
- Paged optimizers, Nvidia'nın unified memory özelliğinden yararlanarak eğitim sırasında bellek patlamalarını yönetir.

---

