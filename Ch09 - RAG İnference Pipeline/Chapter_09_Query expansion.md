# Tipik Bir Erişim Adımındaki Problem (The Problem in a Typical Retrieval Step)

Tipik bir erişim adımındaki problem, orijinal sorunuzun tek bir vektör gösterimini kullanarak vektör veritabanınızı sorgulamanızdır (querying your vector DB using a single vector representation). Bu yaklaşım, yalnızca yerleştirme alanının (embedding space) küçük bir alanını kapsar ve bu da sınırlayıcı olabilir. Yerleştirme, sorgunuzun gerekli tüm bilgilerini veya nüanslarını içermiyorsa, alınan içerik (retrieved context) alakalı olmayabilir. Bu, anlamsal olarak ilgili ancak sorgu vektörüne yakın olmayan temel belgelerin göz ardı edilebileceği anlamına gelir. Çözüm, bu sınırlamayı aşmak için bir yol sunan sorgu genişletmesine (query expansion) dayanmaktadır. İlk sorununuz temelinde birden fazla sorgu oluşturmak için bir LLM (Large Language Model) kullanarak, sorgunuzun farklı yönlerini yakalayan çeşitli perspektifler oluşturursunuz. Bu genişletilmiş sorgular, yerleştirme alanının orijinal sorunuzla hala ilgili olan diğer alanlarını hedefler. Bu, vektör veritabanından daha alakalı belgeler alma olasılığını artırır.

Sorgu genişletme, çoklu sorgu (multi-query) olarak da bilinir, ancak ilkeler aynıdır. Örneğin, LangChain'in MultiQueryRetriver adlı uygulaması şu adreste bulunabilir: https://python.langchain.com/docs/how_to/MultiQueryRetriever/

Şimdi kodu inceleyelim. Sorgu genişletme için gerekli modülleri ve sınıfları içe aktararak başlıyoruz:
```python
from langchain_openai import ChatOpenAI
from llm_engineering.domain.queries import Query
from llm_engineering.settings import settings
from .base import RAGStep
from .prompt_templates import QueryExpansionTemplate
```
Ardından, genişletilmiş sorgu sürümleri oluşturan QueryExpansion sınıfını tanımlarız. Sınıf uygulaması şu adreste bulunabilir: https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/application/rag/query_expanison.py
```python
class QueryExpansion(RAGStep):
    def generate(self, query: Query, expand_to_n: int) -> list[Query]:
        assert expand_to_n > 0, f"'expand_to_n' should be greater than 0. Got {expand_to_n}."
        if self._mock:
            return [query for _ in range(expand_to_n)]
```
generate metodu içinde, önce genişletme sayısının (expand_to_n) sıfırdan büyük olduğundan emin oluruz. Eğer örnek mock modundaysa (self._mock True ise), API'yi gerçekten çağırmadan genişletmeyi simüle etmek için orijinal sorgunun kopyalarını içeren bir liste döndürür.

Eğer mock modunda değilsek, prompt'u oluşturmaya ve dil modelini başlatmaya devam ederiz:
```python
query_expansion_template = QueryExpansionTemplate()
prompt = query_expansion_template.create_template(expand_to_n - 1)
model = ChatOpenAI(model=settings.OPENAI_MODEL_ID, api_key=settings.OPENAI_API_KEY, temperature=0)
```
Burada, QueryExpansionTemplate örneği oluştururuz ve genişlet_to_n - 1 yeni sorgu oluşturmak için özelleştirilmiş bir prompt yaratırız (orijinali hariç). Belirtilen ayarlarla ChatOpenAI modelini başlatırız ve deterministik çıktı için sıcaklığı (temperature) 0 olarak ayarlarız.

Ardından, prompt'u modelle birleştirerek bir LangChain zinciri oluştururuz ve kullanıcının sorusuyla çağırırız:
```python
chain = prompt | model
response = chain.invoke({"question": query})
result = response.content
```
Prompt'u modele pipe ederek (prompt | model), orijinal sorguyla çağırıldığında genişletilmiş sorgular oluşturan bir zincir kurarız. Modelin yanıtı result nesnesinde yakalanır.

Yanıtı aldıktan sonra, genişletilmiş sorguları ayrıştırır ve temizleriz:
```python
queries_content = result.strip().split(query_expansion_template.separator)
queries = [query]
queries += [
    query.replace_content(stripped_content) for content in queries_content if (stripped_content := content.strip())
]
return queries
```
Sonucu, şablonda tanımlanan ayırıcı (separator) kullanarak bireysel sorgulara ayırırız. Orijinal sorguyu içeren bir liste ile başlarız ve fazladan boşlukları temizledikten sonra her genişletilmiş sorguyu ekleriz.

Son olarak, sorgu genişletme için kullanılan prompt'u oluşturan QueryExpansionTemplate sınıfını tanımlarız. Sınıf ve diğer prompt şablonları şu adreste bulunabilir: https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/application/rag/prompt_templates.py
```python
class QueryExpansionTemplate(PromptTemplateFactory):
    prompt: str = """You are an AI language assistant. Your task is to generate {expand_to_n} different versions of the given user question to retrieve relevant documents from a vector database. By generating multiple perspectives on the user question, your goal is to help the user overcome some of the limitations of the distance-based similarity search. Provide these alternative questions separated by '{separator}'. Original question: {question}"""
    @property
    def separator(self) -> str:
        return "#next-question#"
    def create_template(self, expand_to_n: int) -> PromptTemplate:
        return PromptTemplate(
            template=self.prompt,
            input_variables=["question"],
            partial_variables={"separator": self.separator, "expand_to_n": expand_to_n},
        )
```
Bu sınıf, dil modeline birden fazla sorgu oluşturma talimatı veren bir prompt tanımlar. {expand_to_n}, {separator} ve {question} gibi yer tutucuları kullanarak prompt'u özelleştirir. PromptTemplate örneğini oluştururken kaç sorgu oluşturmak istediğimizi tanımlamak için expand_to_n'ı girdi parametresi olarak alır. separator özelliği, oluşturulan sorguları ayırmak için benzersiz bir dize sağlar. expand_to_n ve separator değişkenleri, partial_variables olarak iletilir ve çalışma zamanında değişmez hale getirilir. {question} yer tutucusu ise LLM zinciri her çağrıldığında değiştirilir.

Şimdi, QueryExpansion sınıfını nasıl kullanacağımıza dair bir örneğe bakalım. Aşağıdaki kodu python -m llm_engineering.application.rag.query_expansion komutunu kullanarak çalıştıralım:
```python
query = Query.from_str("Write an article about the best types of advanced RAG methods.")
query_expander = QueryExpansion()
expanded_queries = query_expander.generate(query, expand_to_n=3)
for expanded_query in expanded_queries:
    logger.info(expanded_query.content)
```
Orijinal sorgunun aşağıdaki varyasyonlarını elde ederiz. Gözlemleyebileceğiniz gibi, sorgu genişletme yöntemi, ilk sorgunun daha fazla ayrıntısını ve farklı perspektiflerini sağlamada başarılı oldu, örneğin gelişmiş RAG yöntemlerinin etkinliğini veya bu yöntemlere genel bir bakışı vurgulama gibi (ilk sorgunun orijinal olduğunu unutmayın):
```
2024-09-18 17:51:33.529 | INFO  - Write an article about the best types of advanced RAG methods.
2024-09-18 17:51:33.529 | INFO  - What are the most effective advanced RAG methods, and how can they be applied?
2024-09-18 17:51:33.529 | INFO  - Can you provide an overview of the top advanced retrieval-augmented generation techniques?
```
Şimdi, bir sonraki erişim öncesi optimizasyon yöntemine geçelim: self-querying.

**Kod Açıklaması**

1. `from langchain_openai import ChatOpenAI`: LangChain kütüphanesinden ChatOpenAI sınıfını içe aktarır. Bu sınıf, OpenAI dil modelini kullanarak sohbet tabanlı uygulamalar oluşturmak için kullanılır.
2. `query_expansion_template = QueryExpansionTemplate()`: QueryExpansionTemplate sınıfından bir örnek oluşturur. Bu sınıf, sorgu genişletme için kullanılan prompt'u tanımlar.
3. `prompt = query_expansion_template.create_template(expand_to_n - 1)`: create_template metodunu çağırarak, genişlet_to_n - 1 yeni sorgu oluşturmak için özelleştirilmiş bir prompt yaratır.
4. `model = ChatOpenAI(model=settings.OPENAI_MODEL_ID, api_key=settings.OPENAI_API_KEY, temperature=0)`: ChatOpenAI modelini, belirtilen OpenAI model kimliği ve API anahtarı ile başlatır. Sıcaklık (temperature) 0 olarak ayarlanır, bu da deterministik çıktı sağlar.
5. `chain = prompt | model`: Prompt'u modelle birleştirerek bir LangChain zinciri oluşturur. Bu zincir, orijinal sorguyla çağırıldığında genişletilmiş sorgular oluşturur.
6. `response = chain.invoke({"question": query})`: Zinciri, kullanıcının sorusuyla çağırır ve yanıtı alır.
7. `result = response.content`: Yanıtın içeriğini alır.
8. `queries_content = result.strip().split(query_expansion_template.separator)`: Sonucu, şablonda tanımlanan ayırıcı (separator) kullanarak bireysel sorgulara ayırır.
9. `queries = [query]`: Orijinal sorguyu içeren bir liste oluşturur.
10. `queries += [query.replace_content(stripped_content) for content in queries_content if (stripped_content := content.strip())]`: Genişletilmiş sorguları, fazladan boşlukları temizledikten sonra listeye ekler.

Bu kod, sorgu genişletme yöntemini kullanarak, orijinal sorgunun farklı varyasyonlarını oluşturur ve bu varyasyonları döndürür.

---

