#OVM (Object-Vector Mapping) Tanımı
OVM terimi, 3. Bölüm'de tartışılan nesne-ilişkisel eşleme (Object-Relational Mapping - ORM) deseninden esinlenmiştir. Yapılandırılmış veriler ve SQL tabloları yerine embedding ve vektör veritabanları (Vector DBs) ile çalıştığımız için buna OVM adını verdik. Aksi takdirde, bir ORM deseninin aynı ilkelerini takip eder. 3. Bölüm'de yaptığımıza benzer şekilde, kendi OVM sürümümüzü uygulayacağız. Özel örneğimiz basit olsa da, OOP en iyi uygulamalarını ve ilkelerini kullanarak modüler ve genişletilebilir sınıflar yazma konusunda güçlü bir örnektir. VectorBaseDocument sınıfının tam uygulaması GitHub'da https://github.com/PacktPublishing/LLM-Engineering/blob/main/llm_engineering/domain/base/vector.py adresinde mevcuttur.

#VectorBaseDocument Sınıfı
OVM temel sınıfımız VectorBaseDocument olarak adlandırılır. Qdrant üzerinde CRUD işlemlerini destekleyecektir. Uygulamamızın taleplerine dayanarak, yalnızca oluşturma ve okuma işlemleriyle sınırlı tuttum, ancak kolayca güncelleme ve silme işlevlerine genişletilebilir.

```python
from pydantic import UUID4, BaseModel
from typing import Generic
from llm_engineering.infrastructure.db.qdrant import connection

T = TypeVar("T", bound="VectorBaseDocument")

class VectorBaseDocument(BaseModel, Generic[T], ABC):
    id: UUID4 = Field(default_factory=uuid.uuid4)

    @classmethod
    def from_record(cls: Type[T], point: Record) -> T:
        _id = UUID(point.id, version=4)
        payload = point.payload or {}
        attributes = {
            "id": _id,
            **payload,
        }
        if cls._has_class_attribute("embedding"):
            payload["embedding"] = point.vector or None
        return cls(**attributes)

    def to_point(self: T, **kwargs) -> PointStruct:
        exclude_unset = kwargs.pop("exclude_unset", False)
        by_alias = kwargs.pop("by_alias", True)
        payload = self.dict(exclude_unset=exclude_unset, by_alias=by_alias, **kwargs)
        _id = str(payload.pop("id"))
        vector = payload.pop("embedding", {})
        if vector and isinstance(vector, np.ndarray):
            vector = vector.tolist()
        return PointStruct(id=_id, vector=vector, payload=payload)
```

### Kod Açıklaması
1. `VectorBaseDocument` sınıfı Pydantic'in `BaseModel` sınıfından türetilmiştir ve vektör veritabanındaki (Vector DB) tek bir kaydın niteliklerini yapılandırmaya yardımcı olur.
2. Her OVM, varsayılan olarak UUID4 ile benzersiz tanımlayıcı olarak başlatılır.
3. `Generic[T]` kullanarak, `VectorBaseDocument` sınıfının alt sınıflarının imzaları verilen sınıfa uyarlanır. Örneğin, `Chunk()` sınıfının `from_record()` metodu, `Chunk` türünü döndürür, bu da statik analizör ve tip denetleyicileri (örneğin, mypy) için oldukça yararlıdır.
4. `from_record()` metodu, Qdrant'ın formatındaki bir veri noktasını Pydantic tabanlı iç yapıya uyarlar.
5. `to_point()` metodu, mevcut örneğin niteliklerini alır ve Qdrant'ın `PointStruct()` formatına uyarlar.

#Toplu Ekleme (Bulk Insert) İşlemi
Toplu ekleme işlemi, her belgeyi (document) bir noktaya (point) eşler ve Qdrant bağlantı örneğini kullanarak tüm noktaları belirli bir koleksiyona yükler.

```python
class VectorBaseDocument(BaseModel, Generic[T], ABC):
    ...

    @classmethod
    def bulk_insert(cls: Type[T], documents: list["VectorBaseDocument"]) -> bool:
        try:
            cls._bulk_insert(documents)
        except exceptions.UnexpectedResponse:
            logger.info(f"Collection '{cls.get_collection_name()}' does not exist. Trying to create the collection and reinsert the documents.")
            cls.create_collection()
            try:
                cls._bulk_insert(documents)
            except exceptions.UnexpectedResponse:
                logger.error(f"Failed to insert documents in '{cls.get_collection_name()}'.")
                return False
        return True

    @classmethod
    def _bulk_insert(cls: Type[T], documents: list["VectorBaseDocument"]) -> None:
        points = [doc.to_point() for doc in documents]
        connection.upsert(collection_name=cls.get_collection_name(), points=points)
```

### Kod Açıklaması
1. `bulk_insert()` metodu, belgeleri toplu olarak ekler ve hata durumunda koleksiyonu oluşturmaya çalışır.
2. `_bulk_insert()` metodu, belgeleri noktalara çevirir ve Qdrant'a yükler.

#Koleksiyon Adının Belirlenmesi
Koleksiyon adı, OVM'i miras alan alt sınıflarda tanımlanan `Config` sınıfından belirlenir.

```python
class VectorBaseDocument(BaseModel, Generic[T], ABC):
    ...

    @classmethod
    def get_collection_name(cls: Type[T]) -> str:
        if not hasattr(cls, "Config") or not hasattr(cls.Config, "name"):
            raise ImproperlyConfigured("The class should define a Config class with the 'name' property that reflects the collection's name.")
        return cls.Config.name
```

### Kod Açıklaması
1. `get_collection_name()` metodu, koleksiyon adını `Config` sınıfından alır.

#Toplu Bulma (Bulk Find) İşlemi
Toplu bulma işlemi, bir koleksiyondaki tüm kayıtları listelemek için kullanılır.

```python
class VectorBaseDocument(BaseModel, Generic[T], ABC):
    ...

    @classmethod
    def bulk_find(cls: Type[T], limit: int = 10, **kwargs) -> tuple[list[T], UUID | None]:
        try:
            documents, next_offset = cls._bulk_find(limit=limit, **kwargs)
        except exceptions.UnexpectedResponse:
            logger.error(f"Failed to search documents in '{cls.get_collection_name()}'.")
            documents, next_offset = [], None
        return documents, next_offset

    @classmethod
    def _bulk_find(cls: Type[T], limit: int = 10, **kwargs) -> tuple[list[T], UUID | None]:
        collection_name = cls.get_collection_name()
        offset = kwargs.pop("offset", None)
        offset = str(offset) if offset else None
        records, next_offset = connection.scroll(
            collection_name=collection_name,
            limit=limit,
            with_payload=kwargs.pop("with_payload", True),
            with_vectors=kwargs.pop("with_vectors", False),
            offset=offset,
            **kwargs,
        )
        documents = [cls.from_record(record) for record in records]
        if next_offset is not None:
            next_offset = UUID(next_offset, version=4)
        return documents, next_offset
```

### Kod Açıklaması
1. `bulk_find()` metodu, bir koleksiyondaki kayıtları listelemek için kullanılır ve hata durumunda boş liste döner.
2. `_bulk_find()` metodu, Qdrant'a kaydırma (scroll) işlemi yapar ve kayıtları iç yapıya çevirir.

#Vektör Benzerlik Araması (Vector Similarity Search)
Vektör benzerlik araması, verilen bir sorgu vektörüne en benzer vektörleri bulmak için kullanılır.

```python
class VectorBaseDocument(BaseModel, Generic[T], ABC):
    ...

    @classmethod
    def search(cls: Type[T], query_vector: list, limit: int = 10, **kwargs) -> list[T]:
        try:
            documents = cls._search(query_vector=query_vector, limit=limit, **kwargs)
        except exceptions.UnexpectedResponse:
            logger.error(f"Failed to search documents in '{cls.get_collection_name()}'.")
            documents = []
        return documents

    @classmethod
    def _search(cls: Type[T], query_vector: list, limit: int = 10, **kwargs) -> list[T]:
        collection_name = cls.get_collection_name()
        records = connection.search(
            collection_name=collection_name,
            query_vector=query_vector,
            limit=limit,
            with_payload=kwargs.pop("with_payload", True),
            with_vectors=kwargs.pop("with_vectors", False),
            **kwargs,
        )
        documents = [cls.from_record(record) for record in records]
        return documents
```

### Kod Açıklaması
1. `search()` metodu, vektör benzerlik araması yapar ve hata durumunda boş liste döner.
2. `_search()` metodu, Qdrant'a arama işlemi yapar ve sonuçları iç yapıya çevirir.

---

