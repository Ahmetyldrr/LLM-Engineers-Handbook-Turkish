#Sorguyu Vektör Uzayına Gömme Problemi (Embedding Query into Vector Space Problem)
Sorgunuzu bir vektör uzayına gömmek (embedding) istediğinizde, kullanım durumunuz (use case) tarafından gerekli olan tüm yönlerin (aspects) gömme vektöründe (embedding vector) yeterli sinyal (signal) ile mevcut olduğunu garanti edemezsiniz. Örneğin, alımınızın (retrieval) kullanıcı girdisinde (user's input) sağlanan etiketlere (tags) bağlı olmasını %100 garanti etmek istiyorsunuz. Ne yazık ki, etiketi vurgulayan gömme içinde bırakılan sinyali kontrol edemezsiniz. Sadece sorgu istemini (query prompt) gömerek, etiketlerin gömme vektöründe yeterince temsil edilip edilmediğini veya diğer vektörlere karşı mesafeyi hesaplarken yeterli sinyale sahip olup olmadığını asla bilemezsiniz. Bu problem, arama sırasında sunmak istediğiniz diğer meta veriler (metadata) için de geçerlidir, örneğin Kimlikler (IDs), isimler (names) veya kategoriler (categories). Çözüm, self-sorgulama (self-querying) kullanarak sorgu içindeki etiketleri veya diğer kritik meta verileri çıkarmak ve bunları vektör araması (vector search) ile birlikte filtre olarak kullanmaktır. Self-sorgulama, iş kullanım durumunuz (business use case) için kritik olan çeşitli meta veri alanlarını (metadata fields), örneğin etiketler, Kimlikler, yorum sayısı, beğeniler, paylaşımlar vb. çıkarmak için bir LLM (Large Language Model) kullanır. Daha sonra, alınan meta verilerin nasıl dikkate alınacağı üzerinde tam kontrole sahip olursunuz. LLM Twin kullanım durumumuzda, yazarın adını (author's name) çıkarır ve bunu bir filtre olarak kullanırız. Self-sorgulamalar, filtrelenmiş vektör aramaları (filtered vector searches) ile el ele çalışır, ki bunları bir sonraki bölümde açıklayacağız.

Şimdi kodu inceleyelim. Gerekli modülleri ve sınıfları içe aktararak başlıyoruz:
```python
from langchain_openai import ChatOpenAI
from llm_engineering.application import utils
from llm_engineering.domain.documents import UserDocument
from llm_engineering.domain.queries import Query
from llm_engineering.settings import settings
from .base import RAGStep
from .prompt_templates import SelfQueryTemplate
```
*   `langchain_openai` modülünden `ChatOpenAI` sınıfını içe aktarıyoruz, bu sınıf OpenAI modelini kullanmak için gerekli.
*   `llm_engineering.application` modülünden `utils` sınıfını içe aktarıyoruz, bu sınıf çeşitli yardımcı fonksiyonlar içerir.
*   `llm_engineering.domain.documents` modülünden `UserDocument` sınıfını içe aktarıyoruz, bu sınıf kullanıcı belgelerini temsil eder.
*   `llm_engineering.domain.queries` modülünden `Query` sınıfını içe aktarıyoruz, bu sınıf sorguları temsil eder.
*   `llm_engineering.settings` modülünden `settings` sınıfını içe aktarıyoruz, bu sınıf çeşitli ayarları içerir.
*   `.base` modülünden `RAGStep` sınıfını içe aktarıyoruz, bu sınıf RAG (Retrieval-Augmented Generation) işleminin temel sınıfıdır.
*   `.prompt_templates` modülünden `SelfQueryTemplate` sınıfını içe aktarıyoruz, bu sınıf self-sorgulama için kullanılan prompt şablonunu temsil eder.

Ardından, `RAGStep` sınıfından miras alan ve `generate()` metotunu uygulayan `SelfQuery` sınıfını tanımlarız:
```python
class SelfQuery(RAGStep):
    def generate(self, query: Query) -> Query:
        if self._mock:
            return query
```
*   `SelfQuery` sınıfı, `RAGStep` sınıfından miras alır ve `generate()` metotunu uygular.
*   `generate()` metodu, bir `Query` nesnesini girdi olarak alır ve işlenmiş bir `Query` nesnesi döndürür.
*   `_mock` özelliği `True` ise, orijinal sorgu nesnesini değiştirmeden döndürürüz. Bu, test ve hata ayıklama sırasında model çağrısını atlamak için kullanılır.

`generate()` metodu içinde, prompt şablonunu oluşturur ve dil modelini (language model) başlatırız:
```python
prompt = SelfQueryTemplate().create_template()
model = ChatOpenAI(model=settings.OPENAI_MODEL_ID, api_key=settings.OPENAI_API_KEY, temperature=0)
```
*   `SelfQueryTemplate` sınıfını kullanarak prompt şablonunu oluştururuz.
*   `ChatOpenAI` sınıfını kullanarak OpenAI modelini başlatırız. Model kimliği (model ID) ve API anahtarı (API key) ayarları `settings` sınıfından alınır. Sıcaklık (temperature) 0 olarak ayarlanır.

Daha sonra, prompt ve modeli bir zincir (chain) içinde birleştirir ve kullanıcı sorgusuyla çağırırız:
```python
chain = prompt | model
response = chain.invoke({"question": query})
user_full_name = response.content.strip("\n ")
```
*   Prompt ve modeli bir zincir içinde birleştiririz.
*   Zinciri kullanıcı sorgusuyla çağırırız ve yanıtı alırız.
*   Yanıttan kullanıcı tam adını (user full name) çıkarır ve boşluk karakterlerini temizleriz.

Kullanıcı bilgilerinin çıkarılıp çıkarılmadığını kontrol ederiz:
```python
if user_full_name == "none":
    return query
```
*   Kullanıcı tam adı "none" ise, orijinal sorgu nesnesini döndürürüz.

Kullanıcı tam adını ilk ve son ad olarak ayırır ve bir `UserDocument` nesnesi alır veya oluştururuz:
```python
first_name, last_name = utils.split_user_full_name(user_full_name)
user = UserDocument.get_or_create(first_name=first_name, last_name=last_name)
```
*   `utils.split_user_full_name()` fonksiyonunu kullanarak kullanıcı tam adını ilk ve son ad olarak ayırırız.
*   `UserDocument.get_or_create()` metotunu kullanarak bir `UserDocument` nesnesi alır veya oluştururuz.

Son olarak, sorgu nesnesini çıkarılan yazar bilgileriyle günceller ve döndürürüz:
```python
query.author_id = user.id
query.author_full_name = user.full_name
return query
```
*   Sorgu nesnesinin `author_id` ve `author_full_name` özelliklerini güncelleriz.
*   Güncellenen sorgu nesnesini döndürürüz.

`SelfQueryTemplate` sınıfı, kullanıcı bilgilerini çıkarmak için kullanılan prompt şablonunu tanımlar:
```python
class SelfQueryTemplate(PromptTemplateFactory):
    prompt: str = """You are an AI language model assistant. Your task is to extract information from a user question. The required information that needs to be extracted is the user name or user id. Your response should consist of only the extracted user name (e.g., John Doe) or id (e.g. 1345256), nothing else. If the user question does not contain any user name or id, you should return the following token: none. For example: QUESTION 1: My name is Paul Iusztin and I want a post about... RESPONSE 1: Paul Iusztin QUESTION 2: I want to write a post about... RESPONSE 2: none QUESTION 3: My user id is 1345256 and I want to write a post about... RESPONSE 3: 1345256 User question: {question}"""
    def create_template(self) -> PromptTemplate:
        return PromptTemplate(template=self.prompt, input_variables=["question"])
```
*   `SelfQueryTemplate` sınıfı, `PromptTemplateFactory` sınıfından miras alır.
*   `prompt` özelliği, kullanıcı bilgilerini çıkarmak için kullanılan prompt şablonunu tanımlar.
*   `create_template()` metodu, prompt şablonunu kullanarak bir `PromptTemplate` nesnesi oluşturur.

Self-sorgulamayı kullanarak, kullanım durumumuz için kritik olan meta verileri açıkça çıkarır ve alım sırasında kullanırız. Bu yaklaşım, bir sorgunun tüm gerekli yönlerini yakalamak için yalnızca gömmelerin (embeddings) anlamsal anlamlarına güvenmenin sınırlamalarını aşar.

#Örnek Kullanım
Aşağıdaki kodu `python -m llm_engineering.application.rag.self_query` CLI komutunu kullanarak çalıştırabiliriz:
```python
query = Query.from_str("I am Paul Iusztin. Write an article about the best types of advanced RAG methods.")
self_query = SelfQuery()
query = self_query.generate(query)
logger.info(f"Extracted author_id: {query.author_id}")
logger.info(f"Extracted author_full_name: {query.author_full_name}")
```
*   `Query.from_str()` metotunu kullanarak bir sorgu nesnesi oluştururuz.
*   `SelfQuery` sınıfını kullanarak self-sorgulama nesnesi oluştururuz.
*   `generate()` metotunu kullanarak sorgu nesnesini işleriz.
*   Çıkarılan yazar kimliğini (author ID) ve tam adını (author full name) loglarız.

Aşağıdaki sonuçları elde ederiz:
```
2024-09-18 18:02:10.362 | INFO - Extracted author_id: 900fec95-d621-4315-84c6-52e5229e0b96
2024-09-18 18:02:10.362 | INFO - Extracted author_full_name: Paul Iusztin
```
*   Yazar kimliği ve tam adı doğru bir şekilde çıkarılmıştır.

Artık self-sorgulamanın nasıl çalıştığını anladığımıza göre, alım optimizasyonu adımında filtrelenmiş vektör araması ile nasıl kullanılabileceğini keşfedebiliriz.

---

