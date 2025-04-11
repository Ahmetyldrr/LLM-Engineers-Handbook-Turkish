# Dağıtım Stratejimizi Kanıtlamak için Basit Bir FastAPI Uygulaması Oluşturma

Dağıtım stratejimizi kanıtlamak için basit bir FastAPI uygulaması oluşturmak üzere, önce aşağıdaki gibi bir FastAPI örneği tanımlamalıyız:
```python
from fastapi import FastAPI
app = FastAPI()
```
Ardından, Pydantic'in `BaseModel` sınıfını kullanarak `QueryRequest` ve `QueryResponse` sınıflarını tanımlarız. Bu sınıflar, FastAPI uç noktalarının (endpoint) istek ve yanıt yapısını temsil eder:
```python
class QueryRequest(BaseModel):
    query: str

class QueryResponse(BaseModel):
    answer: str
```
Artık FastAPI bileşenlerimizi tanımladığımıza ve tüm SageMaker öğelerimizi yerine koyduğumuza göre, 9. Bölüm'de sunduğumuz ve ince-tuned LLM'imizi dağıtmadığımız için çalıştıramadığımız `call_llm_service()` ve `rag()` fonksiyonlarını yeniden ele alalım. Bu nedenle, bir hatırlatma olarak, `call_llm_service()` fonksiyonu, SageMaker LLM mikroservisini çağırmak için kullanılan çıkarım (inference) mantığını sarar:
```python
def call_llm_service(query: str, context: str | None) -> str:
    llm = LLMInferenceSagemakerEndpoint(
        endpoint_name=settings.SAGEMAKER_ENDPOINT_INFERENCE, 
        inference_component_name=None
    )
    answer = InferenceExecutor(llm, query, context).execute()
    return answer
```
Ardından, tüm RAG iş mantığını uygulayan `rag()` fonksiyonunu tanımlarız. Kendimizi tekrar etmemek için, fonksiyonun tam açıklaması için 9. Bölüm'e bakınız. Vurgulanması gereken önemli nokta, `rag()` fonksiyonunun yalnızca RAG yapmak için gereken iş adımlarını uyguladığıdır, ki bunlar CPU ve I/O ile sınırlıdır (CPU- and I/O-bounded). Örneğin, `ContextRetriever` sınıfı, OpenAI ve Qdrant'a API çağrıları yapar, ki bunlar ağ I/O'su ile sınırlıdır (network I/O bounded), ve doğrudan CPU'da çalışan embedding modelini çağırır. Ayrıca, LLM çıkarım mantığı farklı bir mikroservise taşındığından, `call_llm_service()` fonksiyonu yalnızca ağ I/O'su ile sınırlıdır. Sonuç olarak, tüm fonksiyon hafifçe çalışır, ağır hesaplama diğer servislerde yapılır, bu da FastAPI sunucusunu düşük gecikme sürelerinde çalıştırmak için hafif ve ucuz bir makinede barındırmamıza olanak tanır:
```python
def rag(query: str) -> str:
    retriever = ContextRetriever(mock=False)
    documents = retriever.search(query, k=3*3)
    context = EmbeddedChunk.to_context(documents)
    answer = call_llm_service(query, context)
    return answer
```
Son olarak, RAG mantığımızı internet üzerinden bir HTTP uç noktası olarak sunmak için kullanılan `rag_endpoint()` fonksiyonunu tanımlarız. FastAPI uygulamasında bir POST uç noktası olarak sunmak için bir Python dekoratörü kullanırız. Bu uç nokta `/rag` rotasına eşlenir ve girdi olarak bir `QueryRequest` bekler. Fonksiyon, kullanıcının sorgusunu `rag` fonksiyonuna çağırarak işler. Başarılı olursa, yanıtı bir `QueryResponse` nesnesi içinde döndürür. Bir istisna oluşursa, HTTP 500 hatası ile istisna ayrıntılarını döndürür:
```python
@app.post("/rag", response_model=QueryResponse)
async def rag_endpoint(request: QueryRequest):
    try:
        answer = rag(query=request.query)
        return {"answer": answer}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e)) from e
```
Bu FastAPI uygulaması, AWS SageMaker'da barındırılan bir LLM'yi bir web hizmetine nasıl etkin bir şekilde entegre edileceğini gösterir ve modelin yanıtlarının alaka düzeyini artırmak için RAG'ı kullanır. Kodun modüler tasarımı, özel sınıflar gibi `ContextRetriever`, `InferenceExecutor` ve `LLMInferenceSagemakerEndpoint` kullanır, kolay özelleştirme ve ölçeklendirme sağlar, bu da üretim ortamlarında ML modellerini dağıtmak için güçlü bir araç haline getirir.

Sunucuyu başlatmak için FastAPI uygulamaları için standart yöntem olan `uvicorn` web sunucusunu kullanacağız. Bunu yapmak için aşağıdaki komutu çalıştırmanız gerekir:
```
uvicorn tools.ml_service:app --host 0.0.0.0 --port 8000 --reload
```
Ayrıca, aynı sonucu elde etmek için aşağıdaki `poe` komutunu çalıştırabilirsiniz:
```
poetry poe run-inference-ml-service
```
`/rag` uç noktasını çağırmak için, FastAPI sunucumuza bir POST HTTP isteği yapmak üzere `curl` CLI komutunu kullanabiliriz:
```
curl -X POST 'http://127.0.0.1:8000/rag' -H 'Content-Type: application/json' -d '{"query": "your_query"}'
```
Her zamanki gibi, gerçek bir kullanıcı sorgusu içeren bir `poe` komutu kullanarak bir örnek sağladık:
```
poetry poe call-inference-ml-service
```
Bu FastAPI sunucusu yalnızca yerel olarak çalışır. Bir sonraki adım, onu AWS Elastic Kubernetes Service (EKS) veya AWS Elastic Container Service (ECS) gibi bir konteyner orkestrasyon servisine dağıtmak olacaktır. Ne yazık ki, bu LLM'lere veya LLMOps'a özgü değildir. Bu nedenle, bu adımları bu kitapta ele almayacağız. Ancak, yapmanız gerekenler hakkında bir fikir edinmek için, bir AWS EKS/ECS kümesi oluşturmanız, FastAPI kodunu Dockerize etmeniz, Docker imajını AWS ECR'ye push etmeniz ve ECR'de barındırılan Docker imajını kullanarak bir ECS/EKR dağıtımı oluşturmanız gerekir. Eğer bu çok fazla geliyorsa, iyi haber şu ki, 11. Bölüm'de ZenML ardışık düzenlerini AWS'ye dağıtmak için benzer bir örnek üzerinden size yol göstereceğiz.

Çıkarım ardışık düzeninizi test ettikten sonra, LLM'yi dağıtmak için kullanılan tüm AWS SageMaker kaynaklarını silmek önemlidir. Neredeyse tüm AWS kaynakları kullanıldıkça ödeme stratejisi kullandığından, SageMaker'ı birkaç saat kullanmak cüzdanınızı bozmaz, ancak unutursanız ve açık bırakırsanız, birkaç gün içinde maliyetler katlanarak artabilir. Bu nedenle, iyi bir kural, SageMaker altyapınızı (veya herhangi bir AWS kaynağını) test ettikten sonra her şeyi silmektir. Neyse ki, sizin için tüm AWS SageMaker kaynaklarını silen bir komut dosyası sağladık:
```
poetry poe delete-inference-endpoint
```
Her şeyin doğru bir şekilde silindiğinden emin olmak için SageMaker kontrol panelinize gidin ve kendiniz kontrol edin.

### Kod Açıklaması

#### `call_llm_service()` Fonksiyonu
```python
def call_llm_service(query: str, context: str | None) -> str:
    llm = LLMInferenceSagemakerEndpoint(
        endpoint_name=settings.SAGEMAKER_ENDPOINT_INFERENCE, 
        inference_component_name=None
    )
    answer = InferenceExecutor(llm, query, context).execute()
    return answer
```
- Bu fonksiyon, SageMaker'da barındırılan LLM mikroservisini çağırmak için kullanılır.
- `LLMInferenceSagemakerEndpoint` sınıfı, SageMaker'daki LLM uç noktasını temsil eder.
- `InferenceExecutor` sınıfı, LLM çıkarımını gerçekleştirmek için kullanılır.

#### `rag()` Fonksiyonu
```python
def rag(query: str) -> str:
    retriever = ContextRetriever(mock=False)
    documents = retriever.search(query, k=3*3)
    context = EmbeddedChunk.to_context(documents)
    answer = call_llm_service(query, context)
    return answer
```
- Bu fonksiyon, RAG iş mantığını uygular.
- `ContextRetriever` sınıfı, sorgu ile ilgili belgeleri aramak için kullanılır.
- `EmbeddedChunk.to_context()` metodu, bulunan belgelerden bir bağlam oluşturur.
- `call_llm_service()` fonksiyonu, LLM çıkarımını gerçekleştirmek için çağrılır.

#### `rag_endpoint()` Fonksiyonu
```python
@app.post("/rag", response_model=QueryResponse)
async def rag_endpoint(request: QueryRequest):
    try:
        answer = rag(query=request.query)
        return {"answer": answer}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e)) from e
```
- Bu fonksiyon, RAG mantığını bir HTTP uç noktası olarak sunar.
- `QueryRequest` nesnesi, gelen istekten sorguyu alır.
- `rag()` fonksiyonu, sorguyu işler ve bir yanıt döndürür.
- Yanıt, bir `QueryResponse` nesnesi içinde döndürülür.

---

