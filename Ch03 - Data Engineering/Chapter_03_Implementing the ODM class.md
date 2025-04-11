#Bir ODM Sınıfını Sıfırdan Uygulama
Bu bölüm, bir ODM (Object Document Mapping) sınıfını sıfırdan nasıl uygulayacağımızı keşfedecek. Bu, ODM'nin nasıl çalıştığını öğrenmek ve modüler ve yeniden kullanılabilir Python sınıfları yazma becerilerimizi keskinleştirmek için mükemmel bir egzersizdir. Bu nedenle, MongoDB veri ambarı ile etkileşimde bulunmak için diğer tüm belgelerin miras alacağı `NoSQLBaseDocument` adında bir temel ODM sınıfı uygulayacağız. Sınıf, `llm_engineering/domain/base/nosql.py` adresindeki depomuzda bulunabilir.

Kod, temel modülleri içe aktararak ve veritabanı bağlantısını kurarak başlar. `_database` değişkeni aracılığıyla, varsayılan olarak `twin` olarak adlandırılan ayarlarda belirtilen veritabanına bir bağlantı kurarız:
```python
import uuid
from abc import ABC
from typing import Generic, Type, TypeVar
from loguru import logger
from pydantic import UUID4, BaseModel, Field
from pymongo import errors
from llm_engineering.domain.exceptions import ImproperlyConfigured
from llm_engineering.infrastructure.db.mongo import connection
from llm_engineering.settings import settings

_database = connection.get_database(settings.DATABASE_NAME)
```
*   `import uuid`: UUID (Universally Unique Identifier) oluşturmak için kullanılan modülü içe aktarır.
*   `from abc import ABC`: Soyut temel sınıflar (Abstract Base Classes) oluşturmak için kullanılan `ABC` sınıfını içe aktarır.
*   `from typing import Generic, Type, TypeVar`: Type hinting için kullanılan `Generic`, `Type` ve `TypeVar` sınıflarını içe aktarır.
*   `from loguru import logger`: Loglama için kullanılan `logger` nesnesini içe aktarır.
*   `from pydantic import UUID4, BaseModel, Field`: Veri doğrulama ve parsing için kullanılan `UUID4`, `BaseModel` ve `Field` sınıflarını içe aktarır.
*   `from pymongo import errors`: PyMongo'nun hata sınıflarını içe aktarır.
*   `_database = connection.get_database(settings.DATABASE_NAME)`: Veritabanı bağlantısını kurar ve `_database` değişkenine atar.

Daha sonra, `NoSQLBaseDocument` sınıfına bağlı bir tip değişkeni `T` tanımlarız. Bu değişken, Python'un generic modülünü kullanarak sınıfın tiplerini genelleştirmemizi sağlar:
```python
T = TypeVar("T", bound="NoSQLBaseDocument")
```
*   `T = TypeVar("T", bound="NoSQLBaseDocument")`: `T` tip değişkenini tanımlar ve `NoSQLBaseDocument` sınıfına bağlı olduğunu belirtir.

`NoSQLBaseDocument` sınıfı, Pydantic'in `BaseModel` sınıfından, Python'un `Generic` sınıfından (yukarıda açıklanan işlevselliği sağlar) ve `ABC` sınıfından (sınıfı soyut hale getirir) miras alır:
```python
class NoSQLBaseDocument(BaseModel, Generic[T], ABC):
```
*   `class NoSQLBaseDocument(BaseModel, Generic[T], ABC)`: `NoSQLBaseDocument` sınıfını tanımlar ve `BaseModel`, `Generic[T]` ve `ABC` sınıflarından miras almasını sağlar.

`NoSQLBaseDocument` sınıfı içinde, bir `id` alanı UUID4 olarak tanımlanır ve varsayılan olarak benzersiz bir UUID oluşturulur. Sınıf ayrıca, örneklerin karşılaştırılmasına ve karma koleksiyonlarda (örneğin, setler veya sözlük anahtarları) kullanılmasına olanak tanıyan `__eq__` ve `__hash__` metotlarını uygular:
```python
id: UUID4 = Field(default_factory=uuid.uuid4)

def __eq__(self, value: object) -> bool:
    if not isinstance(value, self.__class__):
        return False
    return self.id == value.id

def __hash__(self) -> int:
    return hash(self.id)
```
*   `id: UUID4 = Field(default_factory=uuid.uuid4)`: `id` alanını UUID4 olarak tanımlar ve varsayılan olarak benzersiz bir UUID oluşturur.
*   `def __eq__(self, value: object) -> bool`: İki örneğin eşit olup olmadığını karşılaştırır.
*   `def __hash__(self) -> int`: Örneğin karma değerini döndürür.

Sınıf, MongoDB belgeleri ve sınıf örnekleri arasında dönüşüm sağlayan metotlar içerir. `from_mongo()` sınıf metodu, MongoDB'den alınan bir sözlüğü sınıfın bir örneğine dönüştürür. `to_mongo()` örnek metodu, model örneğini MongoDB'ye uygun bir sözlüğe dönüştürür:
```python
@classmethod
def from_mongo(cls: Type[T], data: dict) -> T:
    if not data:
        raise ValueError("Data is empty.")
    id = data.pop("_id")
    return cls(**dict(data, id=id))

def to_mongo(self: T, **kwargs) -> dict:
    exclude_unset = kwargs.pop("exclude_unset", False)
    by_alias = kwargs.pop("by_alias", True)
    parsed = self.model_dump(exclude_unset=exclude_unset, by_alias=by_alias, **kwargs)
    if "_id" not in parsed and "id" in parsed:
        parsed["_id"] = str(parsed.pop("id"))
    for key, value in parsed.items():
        if isinstance(value, uuid.UUID):
            parsed[key] = str(value)
    return parsed
```
*   `@classmethod def from_mongo(cls: Type[T], data: dict) -> T`: MongoDB'den alınan bir sözlüğü sınıfın bir örneğine dönüştürür.
*   `def to_mongo(self: T, **kwargs) -> dict`: Model örneğini MongoDB'ye uygun bir sözlüğe dönüştürür.

`save()` metodu, model örneğini bir MongoDB koleksiyonuna ekler:
```python
def save(self: T, **kwargs) -> T | None:
    collection = _database[self.get_collection_name()]
    try:
        collection.insert_one(self.to_mongo(**kwargs))
        return self
    except errors.WriteError:
        logger.exception("Failed to insert document.")
        return None
```
*   `def save(self: T, **kwargs) -> T | None`: Model örneğini bir MongoDB koleksiyonuna ekler.

`get_or_create()` sınıf metodu, sağlanan filtre seçeneklerine göre bir belge bulmaya çalışır. Eşleşen bir belge bulunursa, sınıfın bir örneğine dönüştürülür. Bulunamazsa, filtre seçenekleriyle yeni bir örnek oluşturulur ve veritabanına kaydedilir:
```python
@classmethod
def get_or_create(cls: Type[T], **filter_options) -> T:
    collection = _database[cls.get_collection_name()]
    try:
        instance = collection.find_one(filter_options)
        if instance:
            return cls.from_mongo(instance)
        new_instance = cls(**filter_options)
        new_instance = new_instance.save()
        return new_instance
    except errors.OperationFailure:
        logger.exception(f"Failed to retrieve document with filter options: {filter_options}")
        raise
```
*   `@classmethod def get_or_create(cls: Type[T], **filter_options) -> T`: Sağlanan filtre seçeneklerine göre bir belge bulmaya çalışır veya yeni bir örnek oluşturur.

`bulk_insert()` sınıf metodu, birden fazla belgeyi veritabanına ekler:
```python
@classmethod
def bulk_insert(cls: Type[T], documents: list[T], **kwargs) -> bool:
    collection = _database[cls.get_collection_name()]
    try:
        collection.insert_many([doc.to_mongo(**kwargs) for doc in documents])
        return True
    except (errors.WriteError, errors.BulkWriteError):
        logger.error(f"Failed to insert documents of type {cls.__name__}")
        return False
```
*   `@classmethod def bulk_insert(cls: Type[T], documents: list[T], **kwargs) -> bool`: Birden fazla belgeyi veritabanına ekler.

`find()` sınıf metodu, sağlanan filtre seçeneklerine göre bir belge arar:
```python
@classmethod
def find(cls: Type[T], **filter_options) -> T | None:
    collection = _database[cls.get_collection_name()]
    try:
        instance = collection.find_one(filter_options)
        if instance:
            return cls.from_mongo(instance)
        return None
    except errors.OperationFailure:
        logger.error("Failed to retrieve document.")
        return None
```
*   `@classmethod def find(cls: Type[T], **filter_options) -> T | None`: Sağlanan filtre seçeneklerine göre bir belge arar.

`bulk_find()` sınıf metodu, sağlanan filtre seçeneklerine göre birden fazla belge arar:
```python
@classmethod
def bulk_find(cls: Type[T], **filter_options) -> list[T]:
    collection = _database[cls.get_collection_name()]
    try:
        instances = collection.find(filter_options)
        return [document for instance in instances if (document := cls.from_mongo(instance)) is not None]
    except errors.OperationFailure:
        logger.error("Failed to retrieve document.")
        return []
```
*   `@classmethod def bulk_find(cls: Type[T], **filter_options) -> list[T]`: Sağlanan filtre seçeneklerine göre birden fazla belge arar.

`get_collection_name()` sınıf metodu, sınıfın ilişkili olduğu MongoDB koleksiyonunun adını belirler:
```python
@classmethod
def get_collection_name(cls: Type[T]) -> str:
    if not hasattr(cls, "Settings") or not hasattr(cls.Settings, "name"):
        raise ImproperlyConfigured("Document should define an Settings configuration class with the name of the collection.")
    return cls.Settings.name
```
*   `@classmethod def get_collection_name(cls: Type[T]) -> str`: Sınıfın ilişkili olduğu MongoDB koleksiyonunun adını belirler.

Her alt sınıf, iç içe `Settings` sınıfını kullanarak yapılandırılabilir. Python ekosisteminde, MongoDB üzerinde çalışan `mongoengine` adlı bir ODM implementasyonu bulunmaktadır. Biz, modüler ve jenerik kod yazma becerilerimizi geliştirmek için kendi ODM'mizi uyguladık.

---

