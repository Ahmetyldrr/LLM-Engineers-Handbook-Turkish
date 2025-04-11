# Bulmacanın Son Parçası: NoSQLBaseDocument Sınıfından Miras Alan Alt Sınıfların Uygulanması

Bulmacanın son parçası, NoSQLBaseDocument temel sınıfından miras alan alt sınıfların uygulanmasını görmektir. Bunlar, veri kategorilerimizi tanımlayan somut sınıflardır. Bu sınıfları, crawler sınıfları içindeki makaleler, depolar ve gönderilerle çalışırken bölüm boyunca kullandınız. 
Gerekli Python modüllerini ve ODM temel sınıfını içe aktararak başlıyoruz: 
```python
from abc import ABC
from typing import Optional
from pydantic import UUID4, Field
from .base import NoSQLBaseDocument
from .types import DataCategory
```
Tüm veri kategorisi türlerini merkezileştirdiğimiz bir enum (enumeration) sınıfı tanımlıyoruz. Bu değişkenler, kitap boyunca tüm ODM sınıflarını yapılandırmada sabitler olarak görev yapacaktır. Sınıf, llm_engineering/domain/types.py deposunda bulunabilir.
```python
from enum import StrEnum

class DataCategory(StrEnum):
    PROMPT = "prompt" 
    QUERIES = "queries" 
    INSTRUCT_DATASET_SAMPLES = "instruct_dataset_samples" 
    INSTRUCT_DATASET = "instruct_dataset" 
    PREFERENCE_DATASET_SAMPLES = "preference_dataset_samples" 
    PREFERENCE_DATASET = "preference_dataset" 
    POSTS = "posts" 
    ARTICLES = "articles" 
    REPOSITORIES = "repositories"
```
Document sınıfı, NoSQLBaseDocument ODM sınıfının üzerinde diğer dokümanlar için soyut bir temel model olarak tanıtıldı. İçerik, platform ve yazar bilgileri gibi ortak öznitelikleri içerir ve ondan miras alacak dokümanlar için standart bir yapı sağlar:
```python
class Document(NoSQLBaseDocument, ABC):
    content: dict 
    platform: str 
    author_id: UUID4 = Field(alias="author_id")
    author_full_name: str = Field(alias="author_full_name")
```
Son olarak, belirli doküman türleri Document sınıfını genişleterek tanımlanır. RepositoryDocument, PostDocument ve ArticleDocument sınıfları, her biri benzersiz alanlara ve ayarlara sahip farklı veri kategorilerini temsil eder ve veritabanındaki ilgili koleksiyon adlarını belirtir:
```python
class RepositoryDocument(Document):
    name: str 
    link: str 

    class Settings:
        name = DataCategory.REPOSITORIES

class PostDocument(Document):
    image: Optional[str] = None 
    link: str | None = None 

    class Settings:
        name = DataCategory.POSTS

class ArticleDocument(Document):
    link: str 

    class Settings:
        name = DataCategory.ARTICLES
```
Son olarak, LLM Twin projesindeki tüm kullanıcıları depolamak ve sorgulamak için kullanılan UserDocument sınıfını tanımlıyoruz:
```python
class UserDocument(NoSQLBaseDocument):
    first_name: str 
    last_name: str 

    class Settings:
        name = "users"

    @property
    def full_name(self):
        return f"{self.first_name} {self.last_name}"
```
NoSQLBaseDocument ODM sınıfını uygulayarak, yalnızca her dokümanın veya alanın (domain entity) alanlarına ve özel işlevlerine odaklanmak zorunda kaldık. Tüm CRUD (Create, Read, Update, Delete) işlevselliği üst sınıfa devredildi. Ayrıca, Pydantic'i alanları tanımlamak için kullanarak, kutudan çıkan tip doğrulamasını (type validation) elde ettik. Örneğin, ArticleDocument sınıfının bir örneğini oluştururken, sağlanan link None veya bir dize değilse, veri geçersiz olduğunda hata atacaktır.

Böylece, ZenML bileşenleriyle başlayan veri toplama işlem hattımızı uygulamasını tamamladık. Ardından, crawlers uygulamasını inceledik ve son olarak ODM sınıfı ve veri kategorisi dokümanlarıyla tamamladık. Son adım, veri toplama işlem hattını çalıştırmak ve ham verileri MongoDB veri ambarına almaktır.

### Kodların Ayrıntılı Açıklaması

1. **İçe Aktarma İşlemleri**
   - `from abc import ABC`: Python'daki soyut temel sınıfları (Abstract Base Classes) kullanmak için ABC sınıfını içe aktarır.
   - `from typing import Optional`: Type hinting için kullanılan Optional tipini içe aktarır. Bir değişkenin None olabileceğini belirtmek için kullanılır.
   - `from pydantic import UUID4, Field`: Pydantic kütüphanesinden UUID4 tipini ve Field fonksiyonunu içe aktarır. UUID4, UUID (Universally Unique Identifier) versiyon 4 tipinde bir değişken tanımlamak için kullanılır. Field, model alanlarını yapılandırmak için kullanılır.
   - `from .base import NoSQLBaseDocument`: NoSQLBaseDocument sınıfını, aynı paketteki base modülünden içe aktarır. Bu sınıf, NoSQL veritabanlarıyla çalışmak için temel bir ODM (Object Document Mapping) sınıfıdır.
   - `from .types import DataCategory`: DataCategory enum sınıfını, aynı paketteki types modülünden içe aktarır. Veri kategorilerini tanımlamak için kullanılır.

2. **DataCategory Enum Sınıfı**
   - `class DataCategory(StrEnum)`: StrEnum tipinde bir enum sınıfı tanımlar. Bu, veri kategorilerini sabit değerler olarak tanımlamak için kullanılır.
   - Enum içindeki her bir değişken (örneğin, `PROMPT`, `QUERIES`), bir veri kategorisini temsil eder.

3. **Document Sınıfı**
   - `class Document(NoSQLBaseDocument, ABC)`: NoSQLBaseDocument'ten miras alan ve ABC olan bir sınıf tanımlar. Bu, dokümanlar için ortak bir temel model sağlar.
   - `content: dict`, `platform: str`, `author_id: UUID4`, `author_full_name: str`: Document sınıfının alanlarıdır. Doküman içeriği, platformu, yazarın ID'si ve yazarın tam adı gibi ortak bilgileri temsil eder.

4. **Belirli Doküman Türleri**
   - `RepositoryDocument`, `PostDocument`, `ArticleDocument`: Document sınıfını genişleterek farklı doküman türlerini tanımlar. Her biri, ilgili veri kategorisine özgü alanlara ve ayarlara sahiptir.

5. **UserDocument Sınıfı**
   - `class UserDocument(NoSQLBaseDocument)`: Kullanıcıları temsil eden bir doküman sınıfıdır. Kullanıcıların ilk adı, soyadı gibi bilgilerini içerir.
   - `@property def full_name(self)`: Kullanıcının tam adını döndüren bir özellik (property) tanımlar.

Bu kodlar, bir NoSQL veritabanı (MongoDB) ile çalışmak üzere tasarlanmış bir ODM sistemini tanımlar. Veri modellerini yapılandırmak ve veri doğrulamasını sağlamak için Pydantic'i kullanır.

---

