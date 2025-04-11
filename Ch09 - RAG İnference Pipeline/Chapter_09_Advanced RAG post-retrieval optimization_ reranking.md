# RAG Sistemlerinde Yeniden Sıralama (Reranking) Problemi ve Çözümü

RAG sistemlerindeki problem, alınan içerik (retrieved context) içerisinde sadece gürültü (noise) ekleyen alakasız parçalar (chunks) içermesidir: 
- Alınan içerik alakasız olabilir, bilgiyi karmaşık hale getirir ve potansiyel olarak dil modelini (language model) karıştırır.
- Gereksiz parçaların dahil edilmesi, istemi (prompt) büyütür ve daha yüksek maliyetlere yol açar. 
- Ayrıca, dil modelleri genellikle içeriğin ilk ve son parçalarına karşı önyargılıdır. Bu nedenle, çok fazla içerik eklerseniz, özünü kaçırma ihtimali yüksektir.
- Parçalar, sorgu (query) ve parça gömmeleri (chunk embeddings) arasındaki benzerliğe göre alınır. 
- Problem, gömme modelinin (embedding model) sorunuzla uyumlu olmayabileceğidir, bu da tamamen alakasız parçalar için yüksek benzerlik puanlarına neden olur.

Çözüm, N × K alınan parçaları ilk soruya göre alaka düzeyine göre sıralamak için yeniden sıralama (reranking) kullanmaktır; burada ilk parça en alakalı, son parça en az alakalı olacaktır. 
N, sorgu genişletmeden (query expansion) sonraki arama sayısını temsil ederken, K, arama başına alınan parça sayısını temsil eder. 
Dolayısıyla, toplam N x K parça alınır.

RAG sistemlerinde yeniden sıralama, alma modeli (retrieval model) tarafından elde edilen ilk sonuçları iyileştiren kritik bir alma sonrası adımdır (post-retrieval step). 
Her bir parçanın orijinal sorguya olan alaka düzeyini, genellikle sinirsel çapraz kodlayıcılar (neural cross-encoders) gibi gelişmiş modeller kullanan yeniden sıralama algoritması uygulayarak değerlendiririz. 
Bu modeller, sorgu ve her bir parça arasındaki anlamsal benzerliği (semantic similarity), gömmelere (embeddings) ve kosinüs benzerlik mesafesine (cosine similarity distance) dayanan ilk alma yöntemlerinden daha doğru bir şekilde değerlendirir.

Sonunda, yeniden sıralama puanına (reranking score) göre N x K öğenin sıralı listesinden en alakalı K parçayı seçeriz.

Yeniden sıralama, sorgu genişletmeyle (query expansion) birleştirildiğinde iyi çalışır. 
İlk olarak, yeniden sıralamanın sorgu genişletme olmadan nasıl çalıştığını anlayalım:
- K'dan fazla parça ara: Potansiyel olarak alakalı bilgilerin daha geniş bir havuzuna sahip olmak için K'dan fazla parça alın.
- Yeniden sıralama kullanarak yeniden sırala: Her bir parçanın sorguya göre gerçek alaka düzeyini değerlendirmek için yeniden sıralamayı bu daha büyük kümeye uygulayın.
- En iyi K'yı seçin: Nihai istemde (final prompt) içerik olarak kullanmak üzere en iyi K parçayı seçin.

Böylece, sorgu genişletmeyle birleştirildiğinde, tek bir konumda K'dan fazla örnek aramak yerine uzaydaki birden fazla noktadan potansiyel olarak değerli içerik toplarız.

Şimdi akış şöyle görünüyor:
- N × K parça ara: Genişletilmiş sorguları kullanarak birden fazla parça kümesi alın.
- Yeniden sıralama kullanarak yeniden sırala: Alınan tüm parçaları alaka düzeyine göre yeniden sıralayın.
- En iyi K'yı seçin: Nihai istem için en alakalı parçaları seçin.

Yeniden sıralamayı RAG ardışık düzenine (pipeline) entegre etmek, alınan içeriğin kalitesini ve alaka düzeyini artırır ve hesaplama kaynaklarını verimli bir şekilde kullanır.

# Kod Açıklaması

Aşağıdaki kod, LLM Twin'in yeniden sıralama adımını uygulamak için kullanılır:
```python
from llm_engineering.application.networks import CrossEncoderModelSingleton
from llm_engineering.domain.embedded_chunks import EmbeddedChunk
from llm_engineering.domain.queries import Query
from .base import RAGStep

class Reranker(RAGStep):
    def __init__(self, mock: bool = False) -> None:
        super().__init__(mock=mock)
        self._model = CrossEncoderModelSingleton()

    def generate(self, query: Query, chunks: list[EmbeddedChunk], keep_top_k: int) -> list[EmbeddedChunk]:
        if self._mock:
            return chunks
        
        # Sorgu içeriği ve her bir parçanın içeriğinden çiftler oluştur
        query_doc_tuples = [(query.content, chunk.content) for chunk in chunks]
        
        # Çapraz kodlayıcı modeli kullanarak her bir çiftin puanını hesapla
        scores = self._model(query_doc_tuples)
        
        # Puanları karşılık gelen parçalarla eşleştir
        scored_query_doc_tuples = list(zip(scores, chunks, strict=False))
        
        # Listeyi puanlara göre azalan sırada sırala
        scored_query_doc_tuples.sort(key=lambda x: x[0], reverse=True)
        
        # En iyi keep_top_k parçayı seç
        reranked_documents = scored_query_doc_tuples[:keep_top_k]
        
        # Parçaları tuple'lardan ayır ve yeniden sıralanmış belgeler olarak döndür
        reranked_documents = [doc for _, doc in reranked_documents]
        return reranked_documents
```

1. `Reranker` sınıfı, `RAGStep` sınıfından miras alır ve yeniden sıralama işlemini gerçekleştirir.
2. `__init__` methodunda, `CrossEncoderModelSingleton` örneği oluşturulur.
3. `generate` methodu, sorgu, parça listesi ve saklanacak üst K sayısı (`keep_top_k`) alır.
4. Eğer mock modu aktifse, orijinal parçaları döndürür.
5. Sorgu içeriği ve her bir parçanın içeriğinden çiftler oluşturur (`query_doc_tuples`).
6. Çapraz kodlayıcı modeli kullanarak her bir çiftin puanını hesaplar (`scores`).
7. Puanları karşılık gelen parçalarla eşleştirir (`scored_query_doc_tuples`).
8. Listeyi puanlara göre azalan sırada sıralar.
9. En iyi `keep_top_k` parçayı seçer (`reranked_documents`).
10. Parçaları tuple'lardan ayır ve yeniden sıralanmış belgeler olarak döndürür.

# CrossEncoderModelSingleton Sınıfı

```python
from sentence_transformers.cross_encoder import CrossEncoder
from .base import SingletonMeta

class CrossEncoderModelSingleton(metaclass=SingletonMeta):
    def __init__(self, model_id: str = settings.RERANKING_CROSS_ENCODER_MODEL_ID, device: str = settings.RAG_MODEL_DEVICE) -> None:
        self._model_id = model_id
        self._device = device
        self._model = CrossEncoder(model_name=self._model_id, device=self._device)
        self._model.model.eval()

    def __call__(self, pairs: list[tuple[str, str]], to_list: bool = True) -> NDArray[np.float32] | list[float]:
        scores = self._model.predict(pairs)
        if to_list:
            scores = scores.tolist()
        return scores
```

1. `CrossEncoderModelSingleton` sınıfı, `SingletonMeta` meta sınıfını kullanarak singleton desenini uygular.
2. `__init__` methodunda, çapraz kodlayıcı modeli belirtilen `model_id` ve `device` ile başlatılır.
3. Model, değerlendirme moduna (`eval()`) ayarlanır.
4. `__call__` methodu, metin çiftlerinin listesini alır ve puanlarını hesaplar.
5. Puanlar, `to_list` parametresine göre liste veya numpy dizisi olarak döndürülür.

Bu sınıf, çapraz kodlayıcı modelini singleton deseninde kullanarak, modelin sadece bir kez yüklenmesini sağlar ve böylece hafıza kullanımını optimize eder.

---

