# Opik ile LLM Uygulamalarında İzleme ve Analiz

İstemimizi izlemek için Comet ML'den Opik'i kullanacağız. Ancak bu bölümün önceki LLMOps bölümünden hatırlayacağınız gibi, yalnızca girdi istemi (input prompt) ve oluşturulan yanıta (generated answer) ilgi duymuyoruz. Kullanıcının girdisinden nihai sonucun elde edilmesine kadar tüm izi (trace) kaydetmek istiyoruz. LLM Twin kullanım durumuna dalmadan önce, daha basit bir örneğe bakalım:

```python
from opik import track
import openai
from opik.integrations.openai import track_openai

openai_client = track_openai(openai.OpenAI())

@track
def preprocess_input(text: str) -> str:
    return text.strip().lower()

@track
def generate_response(prompt: str) -> str:
    response = openai_client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": prompt}]
    )
    return response.choices[0].message.content

@track
def postprocess_output(response: str) -> str:
    return response.capitalize()

@track(name="llm_chain")
def llm_chain(input_text: str) -> str:
    preprocessed = preprocess_input(input_text)
    generated = generate_response(preprocessed)
    postprocessed = postprocess_output(generated)
    return postprocessed

result = llm_chain("Hello, do you enjoy reading the book?")
```

**Kod Açıklaması:**

1. `from opik import track`: Opik kütüphanesinden `track` dekoratörünü içe aktarır. Bu dekoratör, fonksiyonların girdi ve çıktılarını izlemek için kullanılır.
2. `import openai`: OpenAI kütüphanesini içe aktarır.
3. `from opik.integrations.openai import track_openai`: Opik'in OpenAI entegrasyonunu içe aktarır.
4. `openai_client = track_openai(openai.OpenAI())`: OpenAI istemcisini Opik ile izlemek için yapılandırır.
5. `@track`: Fonksiyonları izlemek için kullanılan dekoratördür.
6. `preprocess_input`, `generate_response`, `postprocess_output`: Sırasıyla girdi ön işleme, yanıt oluşturma ve çıktı son işleme fonksiyonlarıdır.
7. `llm_chain`: Ana fonksiyon olup, girdi metnini alır ve nihai sonucu döndürür.

Bu kod, çoğu LLM uygulamasının nasıl görüneceğini basit bir şekilde yansıtmaktadır. `@track()` dekoratörü kullanarak her fonksiyonun girdi ve çıktısını kaydederiz, bu da nihai olarak tek bir iz (trace) halinde toplanacaktır.

**İzleme ve Analiz**

İzleme işleminin son adımı, kullanım durumunuz için gerekli meta verileri mevcut iz (trace) ile ilişkilendirmektir. Aşağıdaki kod parçasında görüldüğü gibi, `update()` metodunu çağırarak izi etiketleyebilir veya Python sözlüğü aracılığıyla girdi token sayısı gibi diğer meta verileri ekleyebilirsiniz:

```python
from opik import track, opik_context

@track
def llm_chain(input_text):
    # LLM zinciri kodu
    # ...
    opik_context.update_current_trace(
        tags=["inference_pipeline"],
        metadata={
            "num_tokens": compute_num_tokens(…)
        },
        feedback_scores=[
            {
                "name": "user_feedback",
                "value": 1.0,
                "reason": "The response was valuable and correct."
            },
            {
                "name": "llm_judge_score",
                "value": compute_llm_judge_score(…),
                "reason": "Computing runtime metrics using an LLM Judge."
            }
        ]
    )
```

**LLM Twin Projesine İzleme Eklemek**

LLM Twin projesine prompt izleme eklemek için, önce Şekil 11.21'e bakalım ve model-serving mimarisini hatırlayalım. İki mikroservisimiz var: LLM ve iş mikroservisleri. LLM mikroservisinin dar bir kapsamı vardır, çünkü yalnızca kullanıcı girdisini ve bağlamı içeren bir istemi girdi olarak alır ve genellikle son işleme tabi tutulan bir yanıt döndürür.

```python
from opik import track

@track
def call_llm_service(query: str, context: str | None) -> str:
    llm = LLMInferenceSagemakerEndpoint(…)
    answer = InferenceExecutor(llm, query, context).execute()
    return answer

@track
def rag(query: str) -> str:
    retriever = ContextRetriever()
    documents = retriever.search(query, k=3 * 3)
    context = EmbeddedChunk.to_context(documents)
    answer = call_llm_service(query, context)
    return answer
```

**Kod Açıklaması:**

1. `call_llm_service`: LLM servisini çağıran fonksiyon.
2. `rag`: Uygulamanın giriş noktası olan fonksiyon.

`@track` dekoratörü kullanarak, `call_llm_service` ve `rag` fonksiyonlarının girdi ve çıktılarını izleriz.

**İzleme ve Analiz için Meta Verilerin Eklenmesi**

İzleme işleminin son adımı, değerli meta verileri ve etiketleri izlerimize eklemektir. Bunu yapmak için, `rag` fonksiyonunu aşağıdaki şekilde geliştireceğiz:

```python
@track
def rag(query: str) -> str:
    retriever = ContextRetriever()
    documents = retriever.search(query, k=3 * 3)
    context = EmbeddedChunk.to_context(documents)
    answer, prompt = call_llm_service(query, context)
    trace = get_current_trace()
    trace.update(
        tags=["rag"],
        metadata={
            "model_id": settings.HF_MODEL_ID,
            "embedding_model_id": settings.TEXT_EMBEDDING_MODEL_ID,
            "temperature": settings.TEMPERATURE_INFERENCE,
            "prompt_tokens": compute_num_tokens(prompt),
            "total_tokens": compute_num_tokens(answer),
        }
    )
    return answer
```

**Sürekli İzlenmesi Gereken Üç Temel Aspekt**

1. **Model Yapılandırması (Model Configuration)**: Hem LLM hem de RAG katmanında kullanılan diğer modellerin yapılandırmasını izlemek önemlidir.
2. **Toplam Token Sayısı (Total Number of Tokens)**: Girdi istemleri tarafından oluşturulan token sayısının ve toplam token sayısının istatistiklerini sürekli olarak analiz etmek önemlidir.
3. **Her Adımın Süresi (Duration of Each Step)**: İz (trace) içindeki her adımın süresini izlemek, sistemdeki darboğazları bulmak için önemlidir.

---

