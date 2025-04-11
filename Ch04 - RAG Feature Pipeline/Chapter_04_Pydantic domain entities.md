# Göndericileri İncelemeden Önce
Göndericileri (dispatchers) incelemeden önce, çalıştığımız alan nesnelerini (domain objects) anlamalıyız. LLM Twin'i uygularken bir ölçüde, alan varlıklarının (domain entities) uygulamanızın çekirdeği olduğunu belirten alan-odaklı tasarım (Domain-Driven Design, DDD) ilkelerini takip ediyoruz. Bu nedenle, ilerlemeden önce, çalıştığımız alan sınıflarının hiyerarşisini anlamak önemlidir. Alan varlıklarının kodu GitHub'da https://github.com/PacktPublishing/LLM-Engineering/tree/main/llm_engineering/domain adresinde mevcuttur. Tüm alan varlıklarımızı modellemek için Pydantic'i kullandık. Kitabı yazarken, Pydantic'i seçmek kesin bir seçimdi, çünkü kutudan çıkan tip doğrulama (type validation) ile veri yapıları yazmak için Python paketi olarak kullanılıyor. Python dinamik olarak yazılmış bir dil olduğundan, çalışma zamanında tip doğrulama için Pydantic'i kullanmak, her zaman doğru veri türüyle çalıştığınızdan emin olabileceğiniz için sisteminizi kat kat daha sağlam hale getirir.

LLM Twin uygulamamızın alanı iki boyuta ayrılmıştır:
- Veri kategorisi (Data category): Gönderi (Post), makale (article) ve depo (repository)
- Verinin durumu (State of the data): Temizlenmiş (Cleaned), parçalanmış (chunked) ve gömülmüş (embedded)

Belgenin her durumu için bir temel sınıf oluşturmaya karar verdik, sonuç olarak aşağıdaki temel soyut sınıflara sahip olduk:
```python
class CleanedDocument(VectorBaseDocument, ABC)
class Chunk(VectorBaseDocument, ABC)
class EmbeddedChunk(VectorBaseDocument, ABC)
```
Hepsinin `VectorBaseDocument` sınıfından miras aldığını unutmayın, bu bizim özel OVM (Object Vector Mapping) uygulamamızdır ve bu bölümün sonraki kısmında açıklayacağız. Ayrıca, ABC'den miras alır, bu da sınıfı soyut hale getirir. Böylece, bu sınıflardan bir nesne başlatamazsınız; yalnızca onlardan miras alabilirsiniz. Bu nedenle temel sınıflar her zaman soyut olarak işaretlenir.

Yukarıdaki her temel soyut sınıf (durumu modelleyen), veri kategorisi boyutunu ekleyen bir alt sınıfa sahip olacaktır. Örneğin, `CleanedDocument` sınıfı aşağıdaki alt sınıflara sahip olacaktır:
```python
class CleanedPostDocument(CleanedDocument)
class CleanedArticleDocument(CleanedDocument)
class CleanedRepositoryDocument(CleanedDocument)
```
Şekil 8.16'da görebileceğimiz gibi, `Chunk` ve `EmbeddedChunk` temel soyut sınıfları için aynı mantığı tekrar edeceğiz. Her veri kategorisi ve durum kombinasyonu için özel bir belge sınıfı uygulayacağız, sonuç olarak dokuz tür alan varlığı elde edeceğiz. Örneğin, ham bir belgeyi işlerken, temizleme adımı bir `CleanedArticleDocument` örneği üretecek, parçalama adımı bir liste `ArticleChunk` nesnesi döndürecek ve gömme işlemi, vektör DB'ye almak için gerekli tüm meta verileri kapsayan `EmbeddedArticleChunk` örnekleri döndürecektir. Aynı şey gönderiler ve depolar için de olacaktır.

# Şekil 4.16: Alan Varlıkları Sınıf Hiyerarşisi ve Etkileşimi
Bu tasarımı seçtik çünkü durum listesi nadiren değişecek ve veri kategorisi listesini genişletmek istiyoruz. Böylece, sınıfları durumdan sonra yapılandırmak, başka bir veri kategorisini bu temel soyut sınıflardan miras alarak takabilmemizi sağlar.

Temizlenmiş belgenin hiyerarşisinin tam kodunu görelim. Temizlenmiş bir belgenin tüm özellikleri vektör DB'nin meta verileri içinde kaydedilecektir. Örneğin, temizlenmiş bir makale belgesinin meta verileri her zaman içeriği, platformu, yazar kimliğini, yazarın tam adını ve makalenin bağlantısını içerecektir.

Bir diğer temel husus, vektör DB içindeki koleksiyonun adını, varlığın veri kategorisini ve koleksiyonu oluştururken vektör indeksini kullanıp kullanmayacağımızı tanımlayan Config iç sınıfıdır:
```python
class CleanedDocument(VectorBaseDocument, ABC):
    content: str
    platform: str
    author_id: UUID4
    author_full_name: str

class CleanedPostDocument(CleanedDocument):
    image: Optional[str] = None

    class Config:
        name = "cleaned_posts"
        category = DataCategory.POSTS
        use_vector_index = False

class CleanedArticleDocument(CleanedDocument):
    link: str

    class Config:
        name = "cleaned_articles"
        category = DataCategory.ARTICLES
        use_vector_index = False

class CleanedRepositoryDocument(CleanedDocument):
    name: str
    link: str

    class Config:
        name = "cleaned_repositories"
        category = DataCategory.REPOSITORIES
        use_vector_index = False
```
Bu bölümü sonlandırmak için, parçanın (chunk) ve gömülmüş parçanın (embedded chunk) temel soyut sınıfına da bakalım:
```python
class Chunk(VectorBaseDocument, ABC):
    content: str
    platform: str
    document_id: UUID4
    author_id: UUID4
    author_full_name: str
    metadata: dict = Field(default_factory=dict)
    # PostChunk, ArticleChunk, RepositoryChunk

class EmbeddedChunk(VectorBaseDocument, ABC):
    content: str
    embedding: list[float] | None
    platform: str
    document_id: UUID4
    author_id: UUID4
    author_full_name: str
    metadata: dict = Field(default_factory=dict)
    # EmbeddedPostChunk, EmbeddedArticleChunk, EmbeddedRepositoryChunk
```
Tüm veri kategorilerimizi tek bir sabit yapısında toplayan bir enum tanımladık:
```python
class DataCategory(StrEnum):
    POSTS = "posts"
    ARTICLES = "articles"
    REPOSITORIES = "repositories"
```
Alan nesnelerinin nasıl çalıştığını tam olarak anlamak için son adım, `VectorBaseDocument` OVM sınıfına zoom yapmaktır.

### Kod Açıklaması

1. `class CleanedDocument(VectorBaseDocument, ABC):`
   - Temizlenmiş belgeler için temel soyut sınıf.
   - `VectorBaseDocument` sınıfından ve `ABC` (Abstract Base Classes) modülünden miras alır.

2. `content: str, platform: str, author_id: UUID4, author_full_name: str`
   - Temizlenmiş bir belgenin temel özellikleri.

3. `class CleanedPostDocument(CleanedDocument):`
   - Gönderiler için temizlenmiş belge sınıfı.
   - `CleanedDocument` sınıfından miras alır ve gönderilere özgü ek özellikler içerir.

4. `class Config:`
   - Vektör DB'deki koleksiyonun konfigürasyonunu tanımlar.
   - `name`: Koleksiyonun adı.
   - `category`: Veri kategorisi.
   - `use_vector_index`: Vektör indeksinin kullanılip kullanılmayacağı.

5. `Chunk` ve `EmbeddedChunk` sınıfları:
   - Parçalanmış ve gömülmüş belgeler için temel soyut sınıflar.
   - Her ikisi de `VectorBaseDocument` sınıfından miras alır.

6. `DataCategory(StrEnum):`
   - Veri kategorilerini temsil eden enum sınıfı.

Her bir sınıf ve özellik, LLM Twin uygulamasının alan modelinin bir parçasını oluşturur ve uygulama içindeki farklı veri türlerini ve durumlarını temsil eder. Bu yapı, uygulamanın genişletilebilirliğini ve esnekliğini sağlar.

---

