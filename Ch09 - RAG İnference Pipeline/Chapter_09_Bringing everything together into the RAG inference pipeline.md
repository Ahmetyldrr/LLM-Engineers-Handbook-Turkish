# RAG Akışının Tam Olarak Uygulanması
RAG akışını tam olarak uygulamak için, retrieval modelinden alınan içerik ile prompt'u oluşturmalı ve LLM'i çağırarak cevabı üretmeliyiz. Bu bölüm, bu iki adımı tartışacak ve her şeyi tek bir `rag()` fonksiyonuna bağlayacaktır. Bu bölümdeki fonksiyonlara https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/infrastructure/inference_pipeline_api.py adresinden ulaşılabilir.

İlk olarak, LLM servisi ile arayüz oluşturmaktan sorumlu olan `call_llm_service()` fonksiyonuna bakalım. Bu fonksiyon, kullanıcının sorgusunu ve opsiyonel olarak içerik (`context`) alır, dil modeli (`LLM`) uç noktasını (`endpoint`) ayarlar, inference'i (`inference`) çalıştırır ve üretilen cevabı döndürür. İçerik (`context`) opsiyoneldir; LLM'i herhangi bir içerik olmadan çağırabilirsiniz.

```python
def call_llm_service(query: str, context: str | None) -> str:
    llm = LLMInferenceSagemakerEndpoint(
        endpoint_name=settings.SAGEMAKER_ENDPOINT_INFERENCE, 
        inference_component_name=None
    )
    answer = InferenceExecutor(llm, query, context).execute()
    return answer
```

Bu fonksiyon, ince ayarlanmış (`fine-tuned`) LLM Twin modeline bir HTTP isteği gönderir. Bu model, bir AWS SageMaker inference uç noktası (`inference endpoint`) olarak barındırılır.

*   `llm = LLMInferenceSagemakerEndpoint(endpoint_name=settings.SAGEMAKER_ENDPOINT_INFERENCE, inference_component_name=None)`: Bu satır, `LLMInferenceSagemakerEndpoint` sınıfının bir örneğini oluşturur. Bu sınıf, SageMaker'da barındırılan LLM modeline erişim sağlar. `endpoint_name` parametresi, SageMaker'daki inference uç noktasının adını belirtir.
*   `answer = InferenceExecutor(llm, query, context).execute()`: Bu satır, `InferenceExecutor` sınıfının bir örneğini oluşturur ve `execute()` metodunu çağırarak inference'i çalıştırır. `InferenceExecutor`, sorguyu (`query`) ve içerik (`context`) kullanarak LLM'den bir cevap üretir.

`call_llm_service()` fonksiyonu, sorgu ve içerik'i `InferenceExecutor` sınıfına geçirirken, bunları bir prompt şablonuna (`prompt template`) dönüştürür.

```python
prompt = f"""
You are a content creator. Write what the user asked you to while using the provided context as the primary source of information for the content.
User query: {query}
Context: {context}
"""
```

Şimdi, `rag()` fonksiyonuna geçelim. Bu fonksiyon, RAG mantığını bir araya getirir. Sorguya (`query`) göre ilgili belgeleri (`documents`) alır, bu belgeleri prompt'a enjekte edilecek içerik (`context`) haline getirir ve LLM'den nihai cevabı (`answer`) alır.

```python
def rag(query: str) -> str:
    retriever = ContextRetriever(mock=False)
    documents = retriever.search(query, k=3)
    context = EmbeddedChunk.to_context(documents)
    answer = call_llm_service(query, context)
    return answer
```

*   `retriever = ContextRetriever(mock=False)`: Bu satır, `ContextRetriever` sınıfının bir örneğini oluşturur. Bu sınıf, sorguya göre ilgili belgeleri almak için kullanılır.
*   `documents = retriever.search(query, k=3)`: Bu satır, `ContextRetriever` örneğinin `search()` metodunu çağırarak sorguya göre ilgili belgeleri alır. `k=3` parametresi, döndürülecek belge sayısını belirtir.
*   `context = EmbeddedChunk.to_context(documents)`: Bu satır, alınan belgeleri (`documents`) prompt'a enjekte edilecek içerik (`context`) haline getirir.
*   `answer = call_llm_service(query, context)`: Bu satır, `call_llm_service()` fonksiyonunu çağırarak LLM'den nihai cevabı (`answer`) alır.

RAG sistemini geliştirmek için birkaç potansiyel iyileştirme vardır:

1.  **Konuşma Hafızası (`Conversation Memory`)**: Kullanıcıların sorgularını ve üretilen cevapları hafızada saklayarak, sohbet robotunun (`chatbot`) tüm konuşmanın farkında olmasını sağlamak.
2.  **Yönlendirici (`Router`)**: Sorguyu (`query`) çok kategorili bir sınıflandırıcı (`multi-category classifier`) kullanarak ilgili veri kategorilerine yönlendirmek.
3.  **Hibrit Arama (`Hybrid Search`)**: Vektör arama (`vector search`) ve anahtar kelime arama (`keyword search`) algoritmalarını birleştirerek arama doğruluğunu (`search accuracy`) artırmak.
4.  **Çoklu İndeks (`Multi-Index`)**: İçerik gömme (`content embedding`) dışında diğer alanları da indeksleyerek vektör arama doğruluğunu artırmak.

Örneğin, Superlinked kütüphanesini kullanarak içerik (`content`) ve platform (`platform`) alanlarını birleştiren bir çoklu indeks (`multi-index`) oluşturabilirsiniz.

```python
from superlinked.framework.common.schema.id_schema_object import IdField
from superlinked.framework.common.schema.schema import schema
from superlinked.framework.common.schema.schema_object import String

@schema
class ArticleSchema:
    id: IdField
    platform: String
    content: String

article = ArticleSchema()
articles_space_content = TextSimilaritySpace(
    text=chunk(article.content, chunk_size=500, chunk_overlap=50),
    model=settings.EMBEDDING_MODEL_ID,
)
articles_space_platform = CategoricalSimilaritySpace(
    category_input=article.platform,
    categories=["medium", "substack", "wordpress"],
    negative_filter=-5.0,
)
article_index = Index(
    [articles_space_content, articles_space_platform],
    fields=[article.author_id],
)
```

Bu iyileştirmeler, RAG sisteminin doğruluğunu ve etkinliğini artırabilir.

---

