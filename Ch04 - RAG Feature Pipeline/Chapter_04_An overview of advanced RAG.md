#Gelişmiş RAG Çerçevesi (Advanced RAG Framework)

Az önce sunduğumuz vanilla RAG çerçevesi (vanilla RAG framework), alınan belgelerin (retrieved documents) kullanıcının sorusuna (user's question) uygunluğu, alınan içerik (retrieved context) kullanıcının sorusunu yanıtlamak için yeterli olup olmadığı, artırılmış isteme (augmented prompt) sadece gürültü ekleyen fazla bilgi olup olmadığı, alma adımının (retrieval step) gecikmesinin (latency) gereksinimlerimizi karşılayıp karşılamadığı ve alınan bilgiler (retrieved information) kullanılarak geçerli bir yanıt oluşturamadığımızda ne yapacağımız gibi, alma ve yanıt oluşturma kalitesini etkileyen birçok temel yönü ele almaz. Yukarıdaki sorulardan iki sonuç çıkarabiliriz. Birincisi, RAG sistemimiz için alınan verilerin (retrieved data) kalitesini ölçebilen ve kullanıcının sorusuna göre yanıtlar üretebilen sağlam bir değerlendirme modülüne (evaluation module) ihtiyacımız var. Bu konuyu 9. Bölüm'de daha ayrıntılı olarak tartışacağız. İkinci sonuç, algoritmada doğrudan alma sınırlamalarını (retrieval limitations) ele almak için RAG çerçevesini geliştirmemiz gerektiğidir. Bu iyileştirmeler gelişmiş RAG (advanced RAG) olarak bilinir.

Vanilla RAG tasarımı (vanilla RAG design), üç farklı aşamada optimize edilebilir:
- **Almadan Önce (Pre-retrieval)**: Bu aşama, veri indeksleme optimizasyonları (data indexing optimizations) ve sorgu optimizasyonları (query optimizations) için verilerinizi nasıl yapılandıracağınız ve önceden işleyeceğiniz (preprocess) üzerine odaklanır.
- **Alma (Retrieval)**: Bu aşama, vektör arama adımını (vector search step) iyileştirmek için embedding modellerini (embedding models) ve meta veri filtrelemeyi (metadata filtering) geliştirmeyi içerir.
- **Almadan Sonra (Post-retrieval)**: Bu aşama, esas olarak alınan belgelerden (retrieved documents) gürültüyü filtrelemenin ve bir LLM'ye (LLM) yanıt oluşturma için beslemeden önce istemi (prompt) sıkıştırmanın farklı yollarını hedefler.

#Şekil 4.5: Gelişmiş RAG'ın Üç Aşaması

Bu bölüm, mevcut tüm gelişmiş RAG yöntemlerinin (advanced RAG methods) kapsamlı bir listesi olmayı amaçlamamaktadır. Amaç, neyin optimize edilebileceği hakkında bir sezgi oluşturmaktır. Sadece metin verilerine (text data) dayalı örnekler kullanacağız, ancak gelişmiş RAG ilkeleri, veri kategorisinden (data category) bağımsız olarak aynı kalır. Şimdi, üç bileşeni de yakından inceleyelim.

**Kod Açıklaması:**

Bu metinde kod bulunmamaktadır. Ancak, başlıkları ve İngilizce teknik terimleri parantez içinde ekleyerek birebir çeviriyi yaptık.

1. `#Gelişmiş RAG Çerçevesi (Advanced RAG Framework)` - Başlık, gelişmiş RAG çerçevesini tanıtır.
2. `Az önce sunduğumuz vanilla RAG çerçevesi (vanilla RAG framework)...` - Vanilla RAG çerçevesinin sınırlamalarını açıklar.
3. `- **Almadan Önce (Pre-retrieval)**: ...` - RAG'ın optimize edilebileceği ilk aşamayı açıklar.
4. `- **Alma (Retrieval)**: ...` - İkinci aşamayı açıklar.
5. `- **Almadan Sonra (Post-retrieval)**: ...` - Üçüncü aşamayı açıklar.
6. `#Şekil 4.5: Gelişmiş RAG'ın Üç Aşaması` - Gelişmiş RAG'ın üç aşamasını gösteren şeklin başlığı.

---

