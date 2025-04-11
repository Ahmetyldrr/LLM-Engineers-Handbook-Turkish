#ContextRetriever Sınıfının Uygulanması
Let's dive into the ContextRetriever class implementation, which orchestrates the retrieval step in our RAG system by integrating all the advanced techniques we previously used: query expansion, self-querying, reranking, and filtered vector search. The class can be found on GitHub at https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/application/rag/retriever.py . 
#RAG Retrieval Modülünün Arama Mantığı
Figure 9.2: Search logic of the RAG retrieval module 
The entry point function of the ContextRetriever class is the search() method, which calls all the advanced steps discussed in this chapter. Figure 9.2 shows in more detail how the search method glues together all the steps required to search results similar to the user’s query. It highlights how the extracted author details from the self-query step are used within the filtered vector search. Also, it zooms in on the search operation itself, where, for each query, we do three searches to the vector DB, looking for articles, posts, or repositories similar to the query. For each search (out of N searches), we want to retrieve a maximum of K results. Thus, we retrieve a maximum of K / 3 items for each data category (as we have three categories). Therefore, when summed up, we will have a list of ≤ K chunks. The retrieved list is ≤ K (and not equal to K) when a particular data category or more returns < K / 3 items after applying the author filters due to missing chunks for that specific author or data category.

#RAG Retrieval Modülünün Sonuç İşleme Akışı
Figure 9.3: Processing the results flow of the RAG retrieval module 
Figure 9.3 illustrates how we process the results returned by the xN searches. As each search returns ≤ K items, we will end up with ≤ N x K chunks that we aggregate into a single list. As some results might overlap between searchers, we must deduplicate the aggregated list to ensure each chunk is unique. Ultimately, we send the results to the rerank model, order them based on their reranking score, and pick the most relevant top K chunks we will use as context for RAG.

Let's understand how everything from Figures 9.2 and 9.3 is implemented in the ContextRetriever class. 
First, we initialize the class by setting up instances of the QueryExpansion , SelfQuery , and Reranker classes: 
```python
class ContextRetriever:
    def __init__(self, mock: bool = False) -> None:
        self._query_expander = QueryExpansion(mock=mock)
        self._metadata_extractor = SelfQuery(mock=mock)
        self._reranker = Reranker(mock=mock)
```
**Kod Açıklaması:**

*   `ContextRetriever` sınıfı `__init__` methodu ile initialize edilir.
*   `QueryExpansion`, `SelfQuery` ve `Reranker` sınıflarının örnekleri oluşturulur.
*   `mock` parametresi `False` olarak ayarlanırsa, gerçek modeller kullanılır.

In the search() method, we convert the user’s input string into a query object. We then use the SelfQuery instance to extract the author_id and author_full_name from the query:
```python
def search(self, query: str, k: int = 3, expand_to_n_queries: int = 3) -> list:
    query_model = Query.from_str(query)
    query_model = self._metadata_extractor.generate(query_model)
    logger.info("Successfully extracted the author_id from the query.",
                 author_id=query_model.author_id,
    )
```
**Kod Açıklaması:**

*   `search` methodu, kullanıcının girdi dizisini bir sorgu nesnesine dönüştürür.
*   `SelfQuery` örneği kullanılarak `author_id` ve `author_full_name` sorgudan çıkarılır.
*   `logger.info` ile author_id bilgisi loglanır.

Next, we expand the query to generate multiple semantically similar queries using the QueryExpansion instance:
```python
n_generated_queries = self._query_expander.generate(query_model, expand_to_n=expand_to_n_queries)
logger.info("Successfully generated queries for search.",
            num_queries=len(n_generated_queries),
)
```
**Kod Açıklaması:**

*   `QueryExpansion` örneği kullanılarak sorgu genişletilir ve birden fazla anlamsal olarak benzer sorgu oluşturulur.
*   `logger.info` ile oluşturulan sorguların sayısı loglanır.

We then perform the search concurrently for all expanded queries using a thread pool. Each query is processed by the _search() method, which we’ll explore shortly. The results are flattened, deduplicated, and collected into a single list:
```python
with concurrent.futures.ThreadPoolExecutor() as executor:
    search_tasks = [executor.submit(self._search, _query_model, k) for _query_model in n_generated_queries]
    n_k_documents = [task.result() for task in concurrent.futures.as_completed(search_tasks)]
    n_k_documents = utils.misc.flatten(n_k_documents)
    n_k_documents = list(set(n_k_documents))
logger.info("All documents retrieved successfully.", num_documents=len(n_k_documents))
```
**Kod Açıklaması:**

*   Genişletilmiş sorgular için arama işlemi thread pool kullanılarak eşzamanlı olarak gerçekleştirilir.
*   Her bir sorgu `_search` methodu tarafından işlenir.
*   Sonuçlar düzleştirilir, yinelenenler kaldırılır ve tek bir liste halinde toplanır.
*   `logger.info` ile alınan belge sayısı loglanır.

After retrieving the documents, we rerank them based on their relevance to the original query and keep only the top k documents:
```python
if len(n_k_documents) > 0:
    k_documents = self.rerank(query, chunks=n_k_documents, keep_top_k=k)
else:
    k_documents = []
return k_documents
```
**Kod Açıklaması:**

*   Belgeler alındıktan sonra, orijinal sorguya göre yeniden sıralanır ve yalnızca en üstteki k belgeler tutulur.
*   `rerank` methodu kullanılarak belgeler yeniden sıralanır.

The _search() method performs the filtered vector search across different data categories like posts, articles, and repositories. It uses the EmbeddingDispatcher to convert the query into an EmbeddedQuery , which includes the query’s embedding vector and any extracted metadata:
```python
def _search(self, query: Query, k: int = 3) -> list[EmbeddedChunk]:
    assert k >= 3, "k should be >= 3"

    def _search_data_category(data_category_odm: type[EmbeddedChunk], embedded_query: EmbeddedQuery) -> list[EmbeddedChunk]:
        if embedded_query.author_id:
            query_filter = Filter(
                must=[
                    FieldCondition(
                        key="author_id", match=MatchValue(
                            value=str(embedded_query.author_id),
                        ),
                    )
                ]
            )
        else:
            query_filter = None
        return data_category_odm.search(
            query_vector=embedded_query.embedding,
            limit=k // 3,
            query_filter=query_filter,
        )

    embedded_query: EmbeddedQuery = EmbeddingDispatcher.dispatch(query)
```
**Kod Açıklaması:**

*   `_search` methodu, farklı veri kategorileri (post, makale, depo gibi) üzerinde filtrelenmiş vektör araması gerçekleştirir.
*   `EmbeddingDispatcher` kullanılarak sorgu, `EmbeddedQuery` nesnesine dönüştürülür.
*   `_search_data_category` fonksiyonu, belirli bir veri kategorisi için arama işlemini gerçekleştirir.

We used the same EmbeddingDispatcher to embed the query as in the RAG feature pipeline to embed the document chunks stored in the vector DB. Using the same class ensures we use the same embedding model at ingestion and query time, which is critical for the retrieval step. We search each data category separately by leveraging the local _search_data_category() function. Within the _search_data_category() function, we apply the filters extracted from the embedded_query object. For instance, if an author_id is present, we use it to filter the search results only to include documents from that author. The results from all categories are then combined:
```python
post_chunks = _search_data_category(EmbeddedPostChunk, embedded_query)
articles_chunks = _search_data_category(EmbeddedArticleChunk, embedded_query)
repositories_chunks = _search_data_category(EmbeddedRepositoryChunk, embedded_query)
retrieved_chunks = post_chunks + articles_chunks + repositories_chunks
return retrieved_chunks
```
**Kod Açıklaması:**

*   Her bir veri kategorisi için arama işlemi `_search_data_category` fonksiyonu kullanılarak gerçekleştirilir.
*   `embedded_query` nesnesinden çıkarılan filtreler uygulanır.
*   Tüm kategorilerden gelen sonuçlar birleştirilir.

Finally, the rerank() method takes the original query and the list of retrieved documents to reorder them based on relevance:
```python
def rerank(self, query: str | Query, chunks: list[EmbeddedChunk], keep_top_k: int) -> list[EmbeddedChunk]:
    if isinstance(query, str):
        query = Query.from_str(query)
    reranked_documents = self._reranker.generate(query=query, chunks=chunks, keep_top_k=keep_top_k)
    logger.info("Documents reranked successfully.", num_documents=len(reranked_documents))
    return reranked_documents
```
**Kod Açıklaması:**

*   `rerank` methodu, orijinal sorgu ve alınan belgeler listesini alır ve belgeleri alaka düzeyine göre yeniden sıralar.
*   `self._reranker.generate` methodu kullanılarak belgeler yeniden sıralanır.

Leveraging the ContextRetriever class, we can retrieve context from any query with only a few lines of code. For example, let’s take a look at the following code snippet, where we call the entire advanced RAG architecture with a simple call to the search() method:
```python
from loguru import logger
from llm_engineering.application.rag.retriever import ContextRetriever

query = """My name is Paul Iusztin. Could you draft a LinkedIn post discussing RAG systems? I'm particularly interested in:
- how RAG works
- how it is integrated with vector DBs and large language models (LLMs)."""

retriever = ContextRetriever(mock=False)
documents = retriever.search(query, k=3)
logger.info("Retrieved documents:")
for rank, document in enumerate(documents):
    logger.info(f"{rank + 1}: {document}")
```
**Kod Açıklaması:**

*   `ContextRetriever` sınıfı kullanılarak herhangi bir sorgudan içerik alınabilir.
*   `search` methodu ile gelişmiş RAG mimarisi çağrılır.

Calling the code from above using the following CLI command: 
```bash
poetry poe call-rag-retrieval-module
```
This outputs the following:
```
2024-09-18 19:01:50.588 | INFO - Retrieved documents:
2024-09-18 19:01:50.588 | INFO - 1: id=UUID('541d6c22-d15a-4e6a-924a-68b7b1e0a330') content='4 Advanced RAG Algorithms You Must Know by Paul Iusztin Implement 4 advanced RAG retrieval techniques to optimize your vector DB searches. Integrate the RAG retrieval module into a production LLM system…' platform='decodingml.substack.com' document_id=UUID('32648f33-87e6-435c-b2d7-861a03e72392') author_id=UUID('900fec95-d621-4315-84c6-52e5229e0b96') author_full_name='Paul Iusztin' metadata={'embedding_model_id': 'sentence-transformers/all-MiniLM-L6-v2', 'embedding_size': 384, 'max_input_length': 256} link='https://decodingml.substack.com/p/the-4-advanced-rag-algorithms-you?r=1ttoeh'
2024-09-18 19:01:50.588 | INFO - 2: id=UUID('5ce78438-1314-4874-8a5a-04f5fcf0cb21') content='Overview of advanced RAG optimization techniquesA production RAG system is split into 3 main components ingestion clean, chunk, embed, and load your data to a vector DBretrieval query your vector DB for …' platform='medium' document_id=UUID('bd9021c9-a693-46da-97e7-0d06760ee6bf') author_id=UUID('900fec95-d621-4315-84c6-52e5229e0b96') author_full_name='Paul Iusztin' metadata={'embedding_model_id': 'sentence-transformers/all-MiniLM-L6-v2', 'embedding_size': 384, 'max_input_length': 256} link='https://medium.com/decodingml/the-4-advanced-rag-algorithms-you-must-know-to-implement-5d0c7f1199d2'
2024-09-18 19:02:45.729 | INFO  - 3: id=UUID('0405a5da-4686-428a-91ca-446b8e0446ff') content='Every Medium article will be its own lesson An End to End Framework for Production Ready LLM Systems by Building Your LLM TwinThe Importance of Data Pipelines in the Era of Generative AIChange Data Capture Enabling Event Driven …' platform='medium' document_id=UUID('bd9021c9-a693-46da-97e7-0d06760ee6bf') author_id=UUID('900fec95-d621-4315-84c6-52e5229e0b96') author_full_name='Paul Iusztin' metadata={'embedding_model_id': 'sentence-transformers/all-MiniLM-L6-v2', 'embedding_size': 384, 'max_input_length': 256} link='https://medium.com/decodingml/the-4-advanced-rag-algorithms-you-must-know-to-implement-5d0c7f1199d2'
```
#Çeviri

#ContextRetriever Sınıfının Uygulanması (ContextRetriever Class Implementation)
RAG sistemimizdeki retrieval adımını, daha önce kullandığımız gelişmiş tekniklerin (sorgu genişletme (query expansion), self-sorgulama (self-querying), yeniden sıralama (reranking) ve filtrelenmiş vektör arama (filtered vector search)) tümünü entegre ederek düzenleyen ContextRetriever sınıfının uygulanmasına dalalım. Sınıf, GitHub'da https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/application/rag/retriever.py adresinde bulunabilir. 
#RAG Retrieval Modülünün Arama Mantığı (Search Logic of the RAG Retrieval Module)
Figure 9.2: RAG retrieval modülünün arama mantığı 
ContextRetriever sınıfının giriş noktası işlevi, bu bölümde tartışılan tüm gelişmiş adımları çağıran search() methodudur. Figure 9.2, arama methodunun kullanıcının sorgusuna benzer sonuçlar aramak için gereken tüm adımları nasıl birbirine bağladığını daha ayrıntılı olarak gösterir. Self-sorgulama adımından çıkarılan yazar ayrıntılarının filtrelenmiş vektör araması içinde nasıl kullanıldığını vurgular. Ayrıca, arama işleminin kendisine yakınlaştırır, burada her sorgu için vektör DB'ye üç arama yaparız, sorguya benzer makaleler, gönderiler veya depolar ararız. Her arama için (N aramadan biri), maksimum K sonuç almak isteriz. Böylece, her veri kategorisi için maksimum K / 3 öğe alırız (üç kategorimiz olduğu için). Bu nedenle, toplandığında, ≤ K parçadan oluşan bir listeye sahip olacağız. Alınan liste, belirli bir veri kategorisi veya daha fazlası, o yazar veya veri kategorisi için eksik parçalar nedeniyle yazar filtreleri uygulandıktan sonra < K / 3 öğe döndürdüğünde ≤ K'dir (K'ya eşit değil).

#RAG Retrieval Modülünün Sonuç İşleme Akışı (Processing the Results Flow of the RAG Retrieval Module)
Figure 9.3: RAG retrieval modülünün sonuç işleme akışı 
Figure 9.3, xN aramalar tarafından döndürülen sonuçların nasıl işlendiğini gösterir. Her arama ≤ K öğe döndürdüğünden, ≤ N x K parçayı tek bir listede toplarız. Bazı sonuçlar arama motorları arasında çakışabileceğinden, her parçanın benzersiz olduğundan emin olmak için toplanan listeyi yinelenenlerden arındırmalıyız. Sonunda, sonuçları yeniden sıralama modeline göndeririz, yeniden sıralama puanlarına göre sıralarız ve RAG için içerik olarak kullanacağımız en alakalı üst K parçayı seçeriz.

Figure 9.2 ve 9.3'teki her şeyin ContextRetriever sınıfında nasıl uygulandığını anlayalım. 
İlk olarak, QueryExpansion , SelfQuery ve Reranker sınıflarının örneklerini ayarlayarak sınıfı başlatırız: 
```python
class ContextRetriever:
    def __init__(self, mock: bool = False) -> None:
        self._query_expander = QueryExpansion(mock=mock)
        self._metadata_extractor = SelfQuery(mock=mock)
        self._reranker = Reranker(mock=mock)
```
**Kod Açıklaması:**

*   `ContextRetriever` sınıfı `__init__` methodu ile initialize edilir.
*   `QueryExpansion`, `SelfQuery` ve `Reranker` sınıflarının örnekleri oluşturulur.
*   `mock` parametresi `False` olarak ayarlanırsa, gerçek modeller kullanılır.

search() methodunda, kullanıcının girdi dizisini bir sorgu nesnesine dönüştürürüz. Ardından, SelfQuery örneğini kullanarak sorgudan author_id ve author_full_name'i çıkarırız:
```python
def search(self, query: str, k: int = 3, expand_to_n_queries: int = 3) -> list:
    query_model = Query.from_str(query)
    query_model = self._metadata_extractor.generate(query_model)
    logger.info("Sorgudan author_id başarıyla çıkarıldı.",
                 author_id=query_model.author_id,
    )
```
**Kod Açıklaması:**

*   `search` methodu, kullanıcının girdi dizisini bir sorgu nesnesine dönüştürür.
*   `SelfQuery` örneği kullanılarak `author_id` ve `author_full_name` sorgudan çıkarılır.
*   `logger.info` ile author_id bilgisi loglanır.

Ardından, QueryExpansion örneğini kullanarak sorguyu genişletiriz ve birden fazla anlamsal olarak benzer sorgu oluştururuz:
```python
n_generated_queries = self._query_expander.generate(query_model, expand_to_n=expand_to_n_queries)
logger.info("Arama için sorgular başarıyla oluşturuldu.",
            num_queries=len(n_generated_queries),
)
```
**Kod Açıklaması:**

*   `QueryExpansion` örneği kullanılarak sorgu genişletilir ve birden fazla anlamsal olarak benzer sorgu oluşturulur.
*   `logger.info` ile oluşturulan sorguların sayısı loglanır.

Daha sonra, genişletilmiş tüm sorgular için thread pool kullanarak eşzamanlı olarak arama gerçekleştiririz. Her sorgu, birazdan inceleyeceğimiz _search() methodu tarafından işlenir. Sonuçlar düzleştirilir, yinelenenler kaldırılır ve tek bir liste halinde toplanır:
```python
with concurrent.futures.ThreadPoolExecutor() as executor:
    search_tasks = [executor.submit(self._search, _query_model, k) for _query_model in n_generated_queries]
    n_k_documents = [task.result() for task in concurrent.futures.as_completed(search_tasks)]
    n_k_documents = utils.misc.flatten(n_k_documents)
    n_k_documents = list(set(n_k_documents))
logger.info("Tüm belgeler başarıyla alındı.", num_documents=len(n_k_documents))
```
**Kod Açıklaması:**

*   Genişletilmiş sorgular için arama işlemi thread pool kullanılarak eşzamanlı olarak gerçekleştirilir.
*   Her bir sorgu `_search` methodu tarafından işlenir.
*   Sonuçlar düzleştirilir, yinelenenler kaldırılır ve tek bir liste halinde toplanır.
*   `logger.info` ile alınan belge sayısı loglanır.

Belgeleri aldıktan sonra, orijinal sorguya göre yeniden sıralarız ve yalnızca en üstteki k belgeyi tutarız:
```python
if len(n_k_documents) > 0:
    k_documents = self.rerank(query, chunks=n_k_documents, keep_top_k=k)
else:
    k_documents = []
return k_documents
```
**Kod Açıklaması:**

*   Belgeler alındıktan sonra, orijinal sorguya göre yeniden sıralanır ve yalnızca en üstteki k belgeler tutulur.
*   `rerank` methodu kullanılarak belgeler yeniden sıralanır.

_search() methodu, post, makale ve depo gibi farklı veri kategorileri üzerinde filtrelenmiş vektör araması gerçekleştirir. Sorguyu EmbeddedQuery nesnesine dönüştürmek için EmbeddingDispatcher'ı kullanır:
```python
def _search(self, query: Query, k: int = 3) -> list[EmbeddedChunk]:
    assert k >= 3, "k should be >= 3"

    def _search_data_category(data_category_odm: type[EmbeddedChunk], embedded_query: EmbeddedQuery) -> list[EmbeddedChunk]:
        if embedded_query.author_id:
            query_filter = Filter(
                must=[
                    FieldCondition(
                        key="author_id", match=MatchValue(
                            value=str(embedded_query.author_id),
                        ),
                    )
                ]
            )
        else:
            query_filter = None
        return data_category_odm.search(
            query_vector=embedded_query.embedding,
            limit=k // 3,
            query_filter=query_filter,
        )

    embedded_query: EmbeddedQuery = EmbeddingDispatcher.dispatch(query)
```
**Kod Açıklaması:**

*   `_search` methodu, farklı veri kategorileri üzerinde filtrelenmiş vektör araması gerçekleştirir.
*   `EmbeddingDispatcher` kullanılarak sorgu, `EmbeddedQuery` nesnesine dönüştürülür.

Tüm kategorilerden gelen sonuçlar birleştirilir:
```python
post_chunks = _search_data_category(EmbeddedPostChunk, embedded_query)
articles_chunks = _search_data_category(EmbeddedArticleChunk, embedded_query)
repositories_chunks = _search_data_category(EmbeddedRepositoryChunk, embedded_query)
retrieved_chunks = post_chunks + articles_chunks + repositories_chunks
return retrieved_chunks
```
**Kod Açıklaması:**

*   Her bir veri kategorisi için arama işlemi `_search_data_category` fonksiyonu kullanılarak gerçekleştirilir.
*   Tüm kategorilerden gelen sonuçlar birleştirilir.

Son olarak, rerank() methodu, orijinal sorgu ve alınan belgeler listesini alır ve belgeleri alaka düzeyine göre yeniden sıralar:
```python
def rerank(self, query: str | Query, chunks: list[EmbeddedChunk], keep_top_k: int) -> list[EmbeddedChunk]:
    if isinstance(query, str):
        query = Query.from_str(query)
    reranked_documents = self._reranker.generate(query=query, chunks=chunks, keep_top_k=keep_top_k)
    logger.info("Belgeler başarıyla yeniden sıralandı.", num_documents=len(reranked_documents))
    return reranked_documents
```
**Kod Açıklaması:**

*   `rerank` methodu, orijinal sorgu ve alınan belgeler listesini alır ve belgeleri alaka düzeyine göre yeniden sıralar.

ContextRetriever sınıfını kullanarak, herhangi bir sorgudan içerik alabiliriz. Örneğin, aşağıdaki kod parçasına bakalım, burada gelişmiş RAG mimarisini search() methoduna basit bir çağrı ile çağırıyoruz:
```python
from loguru import logger
from llm_engineering.application.rag.retriever import ContextRetriever

query = """My name is Paul Iusztin. Could you draft a LinkedIn post discussing RAG systems? I'm particularly interested in:
- how RAG works
- how it is integrated with vector DBs and large language models (LLMs)."""

retriever = ContextRetriever(mock=False)
documents = retriever.search(query, k=3)
logger.info("Alınan belgeler:")
for rank, document in enumerate(documents):
    logger.info(f"{rank + 1}: {document}")
```
**Kod Açıklaması:**

*   `ContextRetriever` sınıfı kullanılarak herhangi bir sorgudan içerik alınabilir.
*   `search` methodu ile gelişmiş RAG mimarisi çağrılır.

Yukarıdaki kodu aşağıdaki CLI komutunu kullanarak çağırın: 
```bash
poetry poe call-rag-retrieval-module
```
Bu, aşağıdaki çıktıyı verir:
```
2024-09-18 19:01:50.588 | INFO - Alınan belgeler:
2024-09-18 19:01:50.588 | INFO - 1: id=UUID('541d6c22-d15a-4e6a-924a-68b7b1e0b330') content='4 Advanced RAG Algorithms You Must Know by Paul Iusztin Implement 4 advanced RAG retrieval techniques to optimize your vector DB searches. Integrate the RAG retrieval module into a production LLM system…' platform='decodingml.substack.com' document_id=UUID('32648f33-87e6-435c-b2d7-861a03e72392') author_id=UUID('900fec95-d621-4315-84c6-52e5229e0b96') author_full_name='Paul Iusztin' metadata={'embedding_model_id': 'sentence-transformers/all-MiniLM-L6-v2', 'embedding_size': 384, 'max_input_length': 256} link='https://decodingml.substack.com/p/the-4-advanced-rag-algorithms/you?r=1ttoeh'
2024-09-18 19:01:50.588 | INFO - 2: id=UUID('5ce78438-1314-4874-8a5a-04f5fcf0cb21') content='Overview of advanced RAG optimization techniquesA production RAG system is split into 3 main components ingestion clean, chunk, embed, and load your data to a vector DBretrieval query your vector DB for …' platform='medium' document_id=UUID('bd9021c9-a693-46da-97e7-0d06760ee6bf') author_id=UUID('900fec95-d621-4315-84c6-52e5229e0b96') author_full_name='Paul Iusztin' metadata={'embedding_model_id': 'sentence-transformers/all-MiniLM-L6-v2', 'embedding_size': 384, 'max_input_length': 256} link='https://medium.com/decodingml/the-4-advanced-rag-algorithms-you-must-know-to-implement-5d0c7f1199d2'
2024-09-18 19:02:45.729 | INFO  - 3: id=UUID('0405a5da-4686-428a-91ca-446b8e0446ff') content='Every Medium article will be its own lesson An End to End Framework for Production Ready LLM Systems by Building Your LLM TwinThe Importance of Data Pipelines in the Era of Generative AIChange Data Capture Enabling Event Driven …' platform='medium' document_id=UUID('bd9021c9-a693-46da-97e7-0d06760ee6bf') author_id=UUID('900fec95-d621-4315-84c6-52e5229e0b96') author_full_name='Paul Iusztin' metadata={'embedding_model_id': 'sentence-transformers/all-MiniLM-L6-v2', 'embedding_size': 384, 'max_input_length': 256} link='https://medium.com/decodingml/the-4-advanced-rag-algorithms-you-must-know-to-implement-5d0c7f1199d2'
```

---

