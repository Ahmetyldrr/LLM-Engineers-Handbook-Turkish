# Vektör Arama ve Filtreleme
Vektör arama, anlamsal benzerliğe (semantic similarity) dayalı olarak ilgili bilgilerin alınmasında çok önemlidir. Ancak, düz vektör arama (plain vector search), hem doğruluk hem de bilgi erişim gecikmesini (latency) etkileyen önemli zorluklar ortaya koyabilir. Bunun temel nedeni, yalnızca vektör gömmelerinin (vector embeddings) sayısal yakınlığına (numerical proximity) dayanması ve bağlamsal (contextual) veya kategorik nüansları (categorical nuances) dikkate almamasıdır. Düz vektör aramasıyla ilgili temel sorunlardan biri, anlamsal olarak benzer ancak bağlamsal olarak alakasız belgelerin (semantically similar but contextually irrelevant documents) alınmasıdır. Vektör gömmeleri genel anlamsal anlamları yakaladığından, dil kalıplarını (language patterns) veya konuları (topics) paylaşan içeriklere yüksek benzerlik puanları (similarity scores) atayabilir, ancak bunlar sorgunun (query) özel niyeti veya kısıtlamaları ile uyumlu olmayabilir. Örneğin, "Java" için yapılan bir arama, yalnızca anlamsal benzerliğe dayanarak programlama dili veya Endonezya adası hakkında belgeler alabilir ve bu da belirsiz veya yanıltıcı sonuçlara yol açabilir. Ayrıca, veri kümesinin (dataset) boyutu arttıkça, düz vektör arama ölçeklenebilirlik sorunlarından (scalability issues) etkilenebilir. Filtreleme eksikliği, arama algoritmasının (search algorithm) tüm vektör uzayında (vector space) benzerlikleri hesaplamak zorunda kalması anlamına gelir ve bu da gecikmeyi önemli ölçüde artırabilir. Bu kapsamlı arama, yanıt sürelerini yavaşlatır ve daha fazla hesaplama kaynağı (computational resources) tüketir, böylece gerçek zamanlı (real-time) veya büyük ölçekli uygulamalar (large-scale applications) için verimsiz hale gelir.

# Filtrelenmiş Vektör Arama
Filtrelenmiş vektör arama (filtered vector search), meta veri etiketleri (metadata tags) veya kategoriler (categories) gibi ek kriterlere göre filtreleme yaparak arama alanını (search space) daraltır ve vektör benzerliklerini hesaplamadan önce arama uzayını küçültür. Bu filtreleri uygulayarak, arama algoritması potansiyel sonuç havuzunu sorgunun niyetiyle bağlamsal olarak uyumlu olanlarla sınırlar. Bu hedeflenmiş yaklaşım, yalnızca anlamsal benzerliklere dayanarak dikkate alınabilecek alakasız belgeleri ortadan kaldırarak doğruluğu artırır. Ayrıca, filtrelenmiş vektör arama, algoritmanın gerçekleştirmesi gereken karşılaştırma sayısını azaltarak gecikmeyi iyileştirir. Daha küçük ve daha ilgili bir veri alt kümesiyle çalışmak, hesaplama yükünü (computational overhead) azaltır ve daha hızlı yanıt sürelerine yol açar. Bu verimlilik, gerçek zamanlı etkileşimler (real-time interactions) gerektiren veya büyük sorguları (large queries) işleyen uygulamalar için çok önemlidir.

# Kod Uygulaması
Örneğin, Qdrant kullanırken, her belgenin meta verilerinde (metadata) eşleşen `author_id` için bir filtre eklemek üzere aşağıdaki kodu uygulamanız gerekir:
```python
from qdrant_client.models import FieldCondition, Filter, MatchValue

records = qdrant_connection.search(
    collection_name="articles",
    query_vector=query_embedding,
    limit=3,
    with_payload=True,
    query_filter=Filter(
        must=[
            FieldCondition(
                key="author_id",
                match=MatchValue(
                    value=str("1234"),
                ),
            )
        ]
    ),
)
```
Kod Açıklaması:
1. `from qdrant_client.models import FieldCondition, Filter, MatchValue`: Qdrant istemci kütüphanesinden gerekli modelleri (`FieldCondition`, `Filter`, `MatchValue`) içe aktarır.
2. `qdrant_connection.search()`: Qdrant bağlantısı üzerinden arama yapar.
   - `collection_name="articles"`: Aramanın yapılacağı koleksiyonun adı.
   - `query_vector=query_embedding`: Sorgu vektör gömmesi (query vector embedding).
   - `limit=3`: Döndürülecek maksimum sonuç sayısı.
   - `with_payload=True`: Arama sonuçlarıyla birlikte belge yüklerini (payloads) döndürür.
   - `query_filter=Filter(...)`: Arama için uygulanacak filtre.
     - `must=[...]`: Filtre koşullarının bir listesi. Tüm koşullar karşılanmalıdır.
       - `FieldCondition(key="author_id", match=MatchValue(value=str("1234")))`:
         - `key="author_id"`: Filtrelenecek alanın adı (`author_id`).
         - `match=MatchValue(value=str("1234"))`: `author_id` alanının değerinin `"1234"` ile eşleşmesi gerektiğini belirtir.

# Sonuç
Düz vektör arama, anlamsal erişim için bir temel sağlarken, pratik uygulamalarda performansı yavaşlatabilir. Filtrelenmiş vektör arama, vektör gömmelerinin güçlü yanlarını bağlamsal filtrelemeyle birleştirerek RAG sistemlerinde daha doğru ve verimli bilgi erişimini sağlar. RAG işlem hattımızı optimize etmenin son adımı, yeniden sıralamayı (reranking) incelemektir.

---

