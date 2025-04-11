# Şimdi RAG Çıkarım İşlem Hattının Genel Akışını Anladığımıza Göre, Gelişmiş RAG Tekniklerini Keşfedelim

Şimdi RAG çıkarım işlem hattının genel akışını anladığımıza göre, geri alma modülümüzde kullandığımız gelişmiş RAG tekniklerini keşfedelim: 
Ön-geri alma adımı (Pre-retrieval step): Sorgu genişletme (Query expansion) ve kendi kendine sorgulama (Self-querying)
Geri alma adımı (Retrieval step): Filtrelenmiş vektör arama (Filtered vector search)
Geri alma sonrası adımı (Post-retrieval step): Yeniden sıralama (Reranking)

Her yöntemi ayrı ayrı incelemeye başlamadan önce, bu bölümde kullanacağımız Python arayüzlerini belirleyelim, bunlar https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/application/rag/base.py adresinde mevcuttur.

İlk olarak, bir istem şablonu fabrikası (PromptTemplate factory) tanımlıyoruz, bu, istem şablonlarını nasıl örnekleyeceğimizi standartlaştırır. Bir arayüz olarak, ABC'den (Abstract Base Class) miras alır ve bir LangChain PromptTemplate örneği döndüren create_template() yöntemini ortaya koyar (exposes). 
LangChain'e çok bağımlı olmaktan kaçınsak da, arka plandaki mühendisliği anlamak için her şeyi kendimiz uygulamak istiyoruz, ancak PromptTemplate sınıfı gibi bazı nesneler, çok fazla işlevselliği gizlemeden geliştirmeyi hızlandırmak için yararlıdır:
```python
from abc import ABC, abstractmethod
from langchain.prompts import PromptTemplate
from pydantic import BaseModel

class PromptTemplateFactory(ABC, BaseModel):
    @abstractmethod
    def create_template(self) -> PromptTemplate:
        pass
```
Ayrıca, sorgu genişletme (Query expansion) ve kendi kendine sorgulama (Self-querying) gibi gelişmiş RAG adımlarının arayüzünü standartlaştırmak için kullanılan bir RAGStep arayüzü tanımlamak istiyoruz. 
Bu adımlar genellikle diğer LLM'lere bağlı olduğundan, geliştirme sırasında maliyetleri ve hata ayıklama süresini azaltmak için bir mock (sahte) özelliği vardır:
```python
from typing import Any
from llm_engineering.domain.queries import Query

class RAGStep(ABC):
    def __init__(self, mock: bool = False) -> None:
        self._mock = mock

    @abstractmethod
    def generate(self, query: Query, *args, **kwargs) -> Any:
        pass
```
Son olarak, gelişmiş RAG için gerekli olan diğer meta verilerle birlikte kullanıcının girdisini sarmalamak (wrap) için Query alan varlığı (domain entity) nasıl modellenmiştir, anlamalıyız. 
Böylece, onun uygulanmasına bakalım. 
İlk olarak, gerekli sınıfları içe aktarıyoruz (import):
```python
from pydantic import UUID4, Field
from llm_engineering.domain.base import VectorBaseDocument
from llm_engineering.domain.types import DataCategory
```
Ardından, VectorBaseDocument nesne-vektör eşlemesi (Object-Vector Mapping (OVM)) sınıfından miras alan Query varlık sınıfını tanımlarız, bu sınıf 4. Bölüm'de tartışılmıştır. 
Böylece, her sorgu kolayca vektör veritabanına (vector DB) kaydedilebilir veya alınabilir:
```python
class Query(VectorBaseDocument):
    content: str
    author_id: UUID4 | None = None
    author_full_name: str | None = None
    metadata: dict = Field(default_factory=dict)

    class Config:
        category = DataCategory.QUERIES
```
Dikkat edilmesi gereken önemli nokta, kullanıcının sorgusunu bir dizi meta veri alanıyla birleştirmek için kullanılan sınıfın özniteliklerdir (attributes):
- content: Giriş sorgusunu içeren bir dizi (string).
- author_id: Bir UUID4 tanımlayıcısı (identifier), vektör arama işlemi içinde yalnızca belirli bir yazar tarafından yazılan parçaları (chunks) almak için filtre olarak kullanılır.
- author_full_name: author_id meta verilerini sorgulamak için kullanılan isteğe bağlı bir dizi (string).
- metadata: Herhangi bir ek meta veri için bir sözlük (dictionary), varsayılan olarak boş bir sözlük olarak başlatılır.

Bir alan sınıfının standart tanımının yanı sıra, doğrudan bir dizeden (string) bir Query örneği oluşturmak için bir from_str() sınıf yöntemi tanımlarız. 
Bu, sorgu nesnesini oluşturmadan önce sorgu dizesini temizlemeyi (örneğin, baştaki veya sondaki boşluk karakterlerini ve satır sonlarını temizlemek) standart hale getirmemize olanak tanır:
```python
@classmethod
def from_str(cls, query: str) -> "Query":
    return Query(content=query.strip("\n "))
```
Ayrıca, replace_content() adlı bir örnek yöntemi vardır, bu yöntem, özgün sorgunun id, author_id, author_full_name ve metadata değerlerini koruyarak güncellenmiş içerikle yeni bir Query örneği oluşturmak için kullanılır:
```python
def replace_content(self, new_content: str) -> "Query":
    return Query(
        id=self.id,
        content=new_content,
        author_id=self.author_id,
        author_full_name=self.author_full_name,
        metadata=self.metadata,
    )
```
Bu, özellikle meta verileri veya tanımlayıcıları kaybetmeden sorgu metnini değiştirmek (örneğin, ön işleme veya normalleştirme sırasında) istediğimizde yararlı olabilir.

Query sınıfını takiben, EmbeddedQuery sınıfını tanımlarız:
```python
class EmbeddedQuery(Query):
    embedding: list[float]

    class Config:
        category = DataCategory.QUERIES
```
EmbeddedQuery sınıfı, Query sınıfını embedding alanı ekleyerek genişletir. 
EmbeddedQuery varlığı, Qdrant (veya başka bir vektör veritabanı) üzerinde vektör arama işlemleri gerçekleştirmek için gerekli olan tüm verileri ve meta verileri kapsüller.

Şimdi RAG çıkarım işlem hattında kullanılan tüm arayüzleri ve yeni alan varlıklarını anladığımıza göre, gelişmiş RAG ön-geri alma optimizasyon tekniklerimize geçelim.

### Kod Açıklaması

1. `PromptTemplateFactory` sınıfı:
   - Bu sınıf, `ABC` ve `BaseModel` sınıflarından miras alır.
   - `create_template` adlı soyut bir yöntem içerir, bu yöntem bir `PromptTemplate` örneği döndürür.

2. `RAGStep` sınıfı:
   - Bu sınıf, `ABC` sınıfından miras alır.
   - Bir `mock` özelliği içerir, bu özellik geliştirme sırasında maliyetleri ve hata ayıklama süresini azaltmak için kullanılır.
   - `generate` adlı soyut bir yöntem içerir, bu yöntem bir `Query` nesnesi ve diğer argümanları alır.

3. `Query` sınıfı:
   - Bu sınıf, `VectorBaseDocument` sınıfından miras alır.
   - `content`, `author_id`, `author_full_name` ve `metadata` adlı öznitelikler içerir.
   - `from_str` adlı bir sınıf yöntemi içerir, bu yöntem bir dizeden bir `Query` örneği oluşturur.
   - `replace_content` adlı bir örnek yöntemi içerir, bu yöntem güncellenmiş içerikle yeni bir `Query` örneği oluşturur.

4. `EmbeddedQuery` sınıfı:
   - Bu sınıf, `Query` sınıfından miras alır.
   - `embedding` adlı bir öznitelik içerir.
   - `EmbeddedQuery` varlığı, vektör arama işlemleri gerçekleştirmek için gerekli olan tüm verileri ve meta verileri kapsüller.

İngilizce teknik terimler parantez içinde eklenmiştir:
- Pre-retrieval step (Ön-geri alma adımı)
- Query expansion (Sorgu genişletme)
- Self-querying (Kendi kendine sorgulama)
- Retrieval step (Geri alma adımı)
- Filtered vector search (Filtrelenmiş vektör arama)
- Post-retrieval step (Geri alma sonrası adımı)
- Reranking (Yeniden sıralama)
- PromptTemplate factory (İstem şablonu fabrikası)
- Object-Vector Mapping (OVM) (Nesne-Vektör Eşlemesi)
- LangChain PromptTemplate (LangChain İstem Şablonu)
- UUID4 (Universally Unique Identifier 4) 
- VectorBaseDocument (Vektör Tabanlı Belge) 
- DataCategory (Veri Kategorisi)

---

