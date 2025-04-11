#İnce Ayar için Veri Toplama ve Hazırlama

İnce ayar için veri tedarikine (procuring data for fine-tuning) geldiğinde, görev-tanımlı (task-specific) ve alan-tanımlı (domain-specific) modeller arasında yaklaşımlar farklılık gösterir. Görev-tanımlı modeller için veri küratörlüğü (data curation) genellikle mevcut veri kümelerinden (existing datasets) istenen görevin örneklerini toplamak veya yeni olanları oluşturmayı içerir. Bu, bir özetleme modeli (summarization model) için orijinal ve özetlenmiş metin çiftlerini toplamak veya bir çeviri modeli (translation model) için farklı dillerde cümleler toplamak anlamına gelebilir. Alan-tanımlı veri küratörlüğü daha zorlayıcı olabilir. Genellikle, ilgili metinleri, araştırma makalelerini, teknik belgeleri (technical documents) ve diğer alan-tanımlı içerikleri toplamak ve doğrulamak için konu uzmanları (subject matter experts) ile işbirliği yapmayı gerektirir. Bazı durumlarda, büyük miktarda uzmanlaşmış bilgiye erişimi olan kuruluşlar veya kurumlarla ortaklık kurmayı içerebilir. Bu verilerin kalitesi ve uygunluğu (relevance), modelin hedef alanındaki içeriği anlaması ve oluşturması için doğrudan etkili olduğu için çok önemlidir. 
Dikkate değer ki, az-shot prompting (few-shot prompting), özellikle görev-tanımlı uygulamalar için ince ayara alternatif bir strateji olarak ortaya çıkmıştır. Bu yaklaşım, büyük ve güçlü modellerin yeteneklerini, girdi istemi (input prompt) içinde istenen görevin birkaç örneğini sağlayarak kullanır. Tüm senaryolarda ince ayar yerine geçmese de (örneğin, yeni bir alan öğrenmek istediğinizde), az-shot prompting, kapsamlı ek eğitimlere ihtiyaç duymadan modelleri yeni görevlere uyarlamanın verimli bir yolu olabilir. 
Uygulamada, görev-tanımlı ve alan-tanımlı modeller arasındaki çizgi bazen bulanıklaşabilir. Örneğin, tıbbi tanı için ince ayar yapılmış bir model hem görev-tanımlı (tanıya odaklanmış) hem de alan-tanımlı (tıbbi bilgide uzmanlaşmış) olarak kabul edilebilir. 
İnce ayar sürecinin birincil amacını anlamak ve yaklaşımı buna göre uyarlamak önemlidir. 
Bu sürecin bu noktasında, kullanım durumumuza uygun bir veri kümesi koleksiyonuna sahip olmalıyız. 
Sonraki adım, kural-tabanlı filtreleme (rule-based filtering), veri çoğaltma (data duplication), veri dekontaminasyonu (data decontamination) ve veri kalitesi değerlendirmesi (data quality evaluation) yoluyla örneklerin kalitesini iyileştirmeyi içerir.

**İngilizce Teknik Terimlerin Türkçe Karşılıkları Parantez İçinde Verilmiştir**

#İnce Ayar için Veri Toplama ve Hazırlama

İnce ayar için veri tedarikine (procuring data for fine-tuning) geldiğinde, görev-tanımlı (task-specific) ve alan-tanımlı (domain-specific) modeller arasında yaklaşımlar farklılık gösterir. 
- `Görev-tanımlı modeller`: Görev-tanımlı modeller için veri küratörlüğü (data curation) genellikle mevcut veri kümelerinden (existing datasets) istenen görevin örneklerini toplamak veya yeni olanları oluşturmayı içerir.
  - `veri küratörlüğü`: Veri toplama ve düzenleme işlemi
- `veri kümeleri`: Mevcut veri toplulukları
- `Görev-tanımlı veri toplama`: 
  - `özetleme modeli`: Özetleme modeli için orijinal ve özetlenmiş metin çiftlerini toplamak 
  - `çeviri modeli`: Çeviri modeli için farklı dillerde cümleler toplamak

Alan-tanımlı veri küratörlüğü daha zorlayıcı olabilir. 
- `Alan-tanımlı veri toplama`: 
  - `konu uzmanları`: Konu hakkında uzman kişilerle işbirliği yapmak
  - `teknik belgeler`: Teknik belgeler, araştırma makaleleri ve diğer alan-tanımlı içerikleri toplamak ve doğrulamak

Bazı durumlarda, büyük miktarda uzmanlaşmış bilgiye erişimi olan kuruluşlar veya kurumlarla ortaklık kurmayı içerebilir. 
- `uzmanlaşmış bilgi`: Özel alanlarda kullanılan bilgi

Bu verilerin kalitesi ve uygunluğu (relevance), modelin hedef alanındaki içeriği anlaması ve oluşturması için doğrudan etkili olduğu için çok önemlidir. 
- `kalite ve uygunluk`: Verilerin ne kadar iyi ve uygun olduğu

Dikkate değer ki, az-shot prompting (few-shot prompting), özellikle görev-tanımlı uygulamalar için ince ayara alternatif bir strateji olarak ortaya çıkmıştır. 
- `az-shot prompting`: Birkaç örnekle modelin öğrenmesi

Bu yaklaşım, büyük ve güçlü modellerin yeteneklerini, girdi istemi (input prompt) içinde istenen görevin birkaç örneğini sağlayarak kullanır. 
- `girdi istemi`: Modele verilen girdi

Tüm senaryolarda ince ayar yerine geçmese de (örneğin, yeni bir alan öğrenmek istediğinizde), az-shot prompting, kapsamlı ek eğitimlere ihtiyaç duymadan modelleri yeni görevlere uyarlamanın verimli bir yolu olabilir. 
- `ince ayar`: Modelin belirli bir görev için ayarlanması

Uygulamada, görev-tanımlı ve alan-tanımlı modeller arasındaki çizgi bazen bulanıklaşabilir. 
- `görev-tanımlı modeller`: Belirli bir göreve odaklanmış modeller
- `alan-tanımlı modeller`: Belirli bir alana odaklanmış modeller

Örneğin, tıbbi tanı için ince ayar yapılmış bir model hem görev-tanımlı (tanıya odaklanmış) hem de alan-tanımlı (tıbbi bilgide uzmanlaşmış) olarak kabul edilebilir. 
- `tıbbi tanı`: Tıbbi tanı için modelin ayarlanması

İnce ayar sürecinin birincil amacını anlamak ve yaklaşımı buna göre uyarlamak önemlidir. 
- `ince ayar süreci`: Modelin ayarlanma süreci

Bu sürecin bu noktasında, kullanım durumumuza uygun bir veri kümesi koleksiyonuna sahip olmalıyız. 
- `kullanım durumu`: Modelin kullanılacağı durum

Sonraki adım, kural-tabanlı filtreleme (rule-based filtering), veri çoğaltma (data duplication), veri dekontaminasyonu (data decontamination) ve veri kalitesi değerlendirmesi (data quality evaluation) yoluyla örneklerin kalitesini iyileştirmeyi içerir.
- `kural-tabanlı filtreleme`: Kurallara göre veri filtreleme
- `veri çoğaltma`: Verilerin çoğaltılması
- `veri dekontaminasyonu`: Verilerin temizlenmesi
- `veri kalitesi değerlendirmesi`: Verilerin kalitesinin değerlendirilmesi

---

