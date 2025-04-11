# The CleaningDataHandler() Stratejisi Arayüzü (Strategy Interface)

The CleaningDataHandler() strategy interface looks as follows: … # Other imports. 
from typing import Generic , TypeVar

```python
from typing import Generic, TypeVar
```

- `from typing import Generic, TypeVar`: Bu satır, Python'ın `typing` modülünden `Generic` ve `TypeVar` sınıflarını içe aktarır. `TypeVar` bir tip değişkeni tanımlamak için kullanılırken, `Generic` sınıfı tip değişkenleri kullanarak jenerik sınıflar tanımlamaya yarar.

DocumentT = TypeVar( "DocumentT" , bound=Document)
CleanedDocumentT = TypeVar( "CleanedDocumentT" , bound=CleanedDocument)

```python
DocumentT = TypeVar("DocumentT", bound=Document)
CleanedDocumentT = TypeVar("CleanedDocumentT", bound=CleanedDocument)
```

- `DocumentT = TypeVar("DocumentT", bound=Document)`: `DocumentT` adında bir tip değişkeni tanımlar ve bu tipin `Document` sınıfından türetilmesini sınırlar (`bound=Document`). 
- `CleanedDocumentT = TypeVar("CleanedDocumentT", bound=CleanedDocument)`: Benzer şekilde, `CleanedDocumentT` tip değişkenini tanımlar ve `CleanedDocument` sınıfına bağlar.

class CleaningDataHandler (ABC, Generic [DocumentT, CleanedDocumentT]):
    @abstractmethod
    def clean ( self, data_model: DocumentT ) -> CleanedDocumentT:
        pass

```python
class CleaningDataHandler(ABC, Generic[DocumentT, CleanedDocumentT]):
    @abstractmethod
    def clean(self, data_model: DocumentT) -> CleanedDocumentT:
        pass
```

- `class CleaningDataHandler(ABC, Generic[DocumentT, CleanedDocumentT]):`: `CleaningDataHandler` adında soyut bir temel sınıf (`ABC`) tanımlar ve jenerik tip parametreleri (`DocumentT` ve `CleanedDocumentT`) alır.
- `@abstractmethod`: Bu dekoratör, `clean` metodunun alt sınıflar tarafından implemente edilmesini zorunlu kılar.
- `def clean(self, data_model: DocumentT) -> CleanedDocumentT:`: `clean` metodu, `DocumentT` tipinde bir `data_model` alır ve `CleanedDocumentT` tipinde bir nesne döner.

Now, for every post, article and repository, we have to implement a different handler, as follows:

# Her Post, Makale ve Depo için Farklı Handler Implementasyonu

class PostCleaningHandler ( CleaningDataHandler ):
    def clean ( self, data_model: PostDocument ) -> CleanedPostDocument:
        return CleanedPostDocument(
            id=data_model.id,
            content=clean_text(" #### ".join(data_model.content.values())),
            … # Copy the rest of the parameters from the data_model object.
        )

```python
class PostCleaningHandler(CleaningDataHandler):
    def clean(self, data_model: PostDocument) -> CleanedPostDocument:
        return CleanedPostDocument(
            id=data_model.id,
            content=clean_text(" #### ".join(data_model.content.values())),
            # ... diğer parametreleri data_model nesnesinden kopyala
        )
```

- `class PostCleaningHandler(CleaningDataHandler):`: `PostCleaningHandler` sınıfı, `CleaningDataHandler` sınıfından türetilir.
- `def clean(self, data_model: PostDocument) -> CleanedPostDocument:`: `clean` metodu, `PostDocument` tipinde bir nesne alır ve `CleanedPostDocument` döner.
- `content=clean_text(" #### ".join(data_model.content.values()))`: `data_model.content` içindeki değerleri `" #### "` ile birleştirir, `clean_text` fonksiyonu ile temizler ve `content` parametresine atar.

class ArticleCleaningHandler ( CleaningDataHandler ):
    def clean ( self, data_model: ArticleDocument ) -> CleanedArticleDocument:
        valid_content = [content for content in data_model.content.values() if content]
        return CleanedArticleDocument(
            id=data_model.id,
            content=clean_text(" #### ".join(valid_content)),
            platform=data_model.platform,
            link=data_model.link,
            author_id=data_model.author_id,
            author_full_name=data_model.author_full_name,
        )

```python
class ArticleCleaningHandler(CleaningDataHandler):
    def clean(self, data_model: ArticleDocument) -> CleanedArticleDocument:
        valid_content = [content for content in data_model.content.values() if content]
        return CleanedArticleDocument(
            id=data_model.id,
            content=clean_text(" #### ".join(valid_content)),
            platform=data_model.platform,
            link=data_model.link,
            author_id=data_model.author_id,
            author_full_name=data_model.author_full_name,
        )
```

- `valid_content = [content for content in data_model.content.values() if content]`: `data_model.content` içindeki boş olmayan değerleri `valid_content` listesine alır.
- `content=clean_text(" #### ".join(valid_content))`: `valid_content` içindeki değerleri temizler ve `content` parametresine atar.

class RepositoryCleaningHandler ( CleaningDataHandler ):
    def clean ( self, data_model: RepositoryDocument ) -> CleanedRepositoryDocument:
        return CleanedRepositoryDocument(
            id=data_model.id,
            content=clean_text(" #### ".join(data_model.content.values())),
            … # Copy the rest of the parameters from the data_model object.
        )

```python
class RepositoryCleaningHandler(CleaningDataHandler):
    def clean(self, data_model: RepositoryDocument) -> CleanedRepositoryDocument:
        return CleanedRepositoryDocument(
            id=data_model.id,
            content=clean_text(" #### ".join(data_model.content.values())),
            # ... diğer parametreleri data_model nesnesinden kopyala
        )
```

The handlers input a raw document domain entity, clean the content, and return a cleaned document. All the handlers use the `clean_text()` function to clean the text.

# Handler'lar Ham Veri Alanlarını Temizler ve Temizlenmiş Veri Döndürür

Out of simplicity, we used the same cleaning technique for all the data categories. Still, in a real-world setup, we would have to further optimize and create a different cleaning function for each data category.

# Basitlik adına, tüm veri kategorileri için aynı temizleme tekniğini kullandık. Ancak gerçek dünya senaryosunda, her veri kategorisi için farklı bir temizleme fonksiyonu oluşturmak gerekebilir.

The strategy pattern makes this a breeze, as we swap the cleaning function in the handlers, and that’s it.

# Strateji pattern'i, handler'larda temizleme fonksiyonunu değiştirmeyi kolaylaştırır.

The cleaning steps applied in the `clean_text()` function are the same ones discussed in Chapter 5 in the Creating an instruction dataset section.

# `clean_text()` fonksiyonunda uygulanan temizleme adımları, Bölüm 5'te "Creating an instruction dataset" bölümünde tartışılan adımlardır.

We don’t want to repeat ourselves. Thus, for a refresher, check out that chapter.

# Kendimizi tekrar etmek istemiyoruz. Bu nedenle, hatırlamak için o bölüme göz atabilirsiniz.

At this point, we mostly care about automating and integrating the whole logic into the RAG feature pipeline.

# Bu noktada, esas olarak tüm mantığı RAG özellik pipeline'ına otomatikleştirmek ve entegre etmekle ilgileniyoruz.

Thus, after operationalizing the ML system, all the cleaned data used for fine-tuning will be accessed from the logical feature store, making it the single source of truth for accessing data.

# Böylece, ML sistemini operasyonelleştirdikten sonra, ince ayar için kullanılan tüm temizlenmiş verilere mantıksal özellik deposundan erişilecek ve bu, verilere erişmek için tek gerçeğin kaynağı olacaktır.

---

