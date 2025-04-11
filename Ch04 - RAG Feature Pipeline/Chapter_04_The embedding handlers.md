#Gömme İşleyicileri (Embedding Handlers)

Gömme işleyicileri (Embedding Handlers), diğerlerinden biraz farklıdır çünkü `EmbeddingDataHandler()` arayüzü (interface) çoğu mantığı içerir (logic). Bu yaklaşımı, gömme modelini (embedding model) çağırdığımızda, çıkarım (inference) sürecini optimize etmek için mümkün olduğunca çok örnek (sample) toplu olarak işleme tabi tutmak istediğimiz için seçtik. Modeli bir GPU'da çalıştırdığımızda, toplu örnekler bağımsız olarak ve paralel olarak işlenir. Böylece, parçaları (chunks) toplu olarak işleyerek, çıkarım sürecini 10 kat veya daha fazla optimize edebiliriz, kullanılan toplu iş boyutu (batch size) ve donanıma (hardware) bağlı olarak. Tek bir veri noktasında (data point) çıkarım yapmak istiyorsanız, bir `embed()` yöntemi (method) uyguladık ve bir `embed_batch()` yöntemi uyguladık. `embed_batch()` yöntemi, parçalanmış belgeleri (chunked documents) girdi olarak alır, içeriklerini bir liste (list) içinde toplar, bunları gömme modeline (embedding model) geçirir ve sonuçları gömülmüş parça etki alanına (embedded chunk domain entity) eşler (map). Eşleme, her veri kategorisi (data category) için özelleştirilmesi gereken `map_model()` soyut yöntemi (abstract method) aracılığıyla yapılır.

```python
# Diğer içe aktarmalar (Other imports)
from typing import Generic, TypeVar, cast
from llm_engineering.application.networks import EmbeddingModelSingleton

ChunkT = TypeVar("ChunkT", bound=Chunk)
EmbeddedChunkT = TypeVar("EmbeddedChunkT", bound=EmbeddedChunk)
embedding_model = EmbeddingModelSingleton()

class EmbeddingDataHandler(ABC, Generic[ChunkT, EmbeddedChunkT]):
    """Tüm gömme veri işleyicileri (embedding data handlers) için soyut sınıf (abstract class).
    Gömme adımı (embedding step) için tüm veri dönüştürme mantığı (data transformations logic) burada yapılır.
    """
    def embed(self, data_model: ChunkT) -> EmbeddedChunkT:
        return self.embed_batch([data_model])[0]

    def embed_batch(self, data_model: list[ChunkT]) -> list[EmbeddedChunkT]:
        embedding_model_input = [data_model.content for data_model in data_model]
        embeddings = embedding_model(embedding_model_input, to_list=True)
        embedded_chunk = [
            self.map_model(data_model, cast(list[float], embedding)) 
            for data_model, embedding in zip(data_model, embeddings, strict=False)
        ]
        return embedded_chunk

    @abstractmethod
    def map_model(self, data_model: ChunkT, embedding: list[float]) -> EmbeddedChunkT:
        pass
```

**Kod Açıklaması:**

1. `ChunkT` ve `EmbeddedChunkT` tür değişkenleri (type variables) tanımlanır, bunlar sırasıyla `Chunk` ve `EmbeddedChunk` sınıflarının alt sınıflarını temsil eder.
2. `EmbeddingDataHandler` soyut sınıfı (abstract class) tanımlanır, bu sınıf `ABC` ve `Generic` sınıflarından miras alır.
3. `embed` yöntemi, tek bir veri noktasını gömme işlemine tabi tutar ve gömülmüş veri noktasını döndürür.
4. `embed_batch` yöntemi, bir liste veri noktasını toplu olarak gömme işlemine tabi tutar ve gömülmüş veri noktalarının listesini döndürür.
5. `map_model` soyut yöntemi, bir veri noktasını ve gömme vektörünü (embedding vector) alır ve gömülmüş veri noktasını döndürür. Bu yöntem her veri kategorisi için özelleştirilmelidir.

#Makale Gömme İşleyici (Article Embedding Handler)

Diğer işleyiciler oldukça benzer olduğu için sadece `ArticleEmbeddingHandler` sınıfının uygulanmasına bakalım. Gördüğünüz gibi, sadece `map_model` yöntemini uygulamak zorundayız, bu yöntem girdi olarak bir parçayı (chunk) alır ve toplu modda (batch mode) gömme vektörünü hesaplar. Amacı, bu bilgiyi `EmbeddedArticleChunk` Pydantic öğesine (entity) eşlemektir.

```python
class ArticleEmbeddingHandler(EmbeddingDataHandler):
    def map_model(self, data_model: ArticleChunk, embedding: list[float]) -> EmbeddedArticleChunk:
        return EmbeddedArticleChunk(
            id=data_model.id,
            content=data_model.content,
            embedding=embedding,
            platform=data_model.platform,
            link=data_model.link,
            document_id=data_model.document_id,
            author_id=data_model.author_id,
            author_full_name=data_model.author_full_name,
            metadata={
                "embedding_model_id": embedding_model.model_id,
                "embedding_size": embedding_model.embedding_size,
                "max_input_length": embedding_model.max_input_length,
            },
        )
```

**Kod Açıklaması:**

1. `ArticleEmbeddingHandler` sınıfı `EmbeddingDataHandler` sınıfından miras alır.
2. `map_model` yöntemi, `ArticleChunk` nesnesini ve gömme vektörünü (embedding vector) alır ve `EmbeddedArticleChunk` nesnesini döndürür.
3. `EmbeddedArticleChunk` nesnesi, `ArticleChunk` nesnesinin özelliklerini (attributes) ve gömme vektörünü içerir.

#Gömme Modeli Tekil (Embedding Model Singleton)

Son adım, `EmbeddingModelSingleton` sınıfının nasıl çalıştığını anlamaktır. Bu sınıf, `SentenceTransformer` sınıfını (Sentence Transformers'dan) saran (wrapper) bir sınıftır ve gömme modelini başlatır (initialize). Harici paketler (external packages) üzerine bir sarmalayıcı (wrapper) yazmak genellikle iyi bir uygulamadır. Böylece, üçüncü taraf aracı (third-party tool) değiştirmek istediğinizde, sadece sarmalayıcının iç mantığını (internal logic) değiştirmek zorunda kalırsınız, tüm kod tabanını (code base) değil.

```python
from sentence_transformers.SentenceTransformer import SentenceTransformer
from llm_engineering.settings import settings
from .base import SingletonMeta

class EmbeddingModelSingleton(metaclass=SingletonMeta):
    def __init__(
        self,
        model_id: str = settings.TEXT_EMBEDDING_MODEL_ID,
        device: str = settings.RAG_MODEL_DEVICE,
        cache_dir: Optional[Path] = None,
    ) -> None:
        self._model_id = model_id
        self._device = device
        self._model = SentenceTransformer(
            self._model_id,
            device=self._device,
            cache_folder=str(cache_dir) if cache_dir else None,
        )
        self._model.eval()

    @property
    def model_id(self) -> str:
        return self._model_id

    @cached_property
    def embedding_size(self) -> int:
        dummy_embedding = self._model.encode("")
        return dummy_embedding.shape[0]

    @property
    def max_input_length(self) -> int:
        return self._model.max_seq_length

    @property
    def tokenizer(self) -> AutoTokenizer:
        return self._model.tokenizer

    def __call__(
        self, input_text: str | list[str], to_list: bool = True
    ) -> NDArray[np.float32] | list[float] | list[list[float]]:
        try:
            embeddings = self._model.encode(input_text)
        except Exception:
            logger.error(f"Error generating embeddings for {self._model_id=} and {input_text=}")
            return [] if to_list else np.array([])
        if to_list:
            embeddings = embeddings.tolist()
        return embeddings
```

**Kod Açıklaması:**

1. `EmbeddingModelSingleton` sınıfı, `SingletonMeta` metaclass'ını kullanarak tanımlanır.
2. `__init__` yöntemi, gömme modelini başlatır (initialize) ve `_model_id`, `_device` ve `_model` özelliklerini (attributes) ayarlar.
3. `model_id`, `embedding_size`, `max_input_length` ve `tokenizer` özellikleri, gömme modelinin özelliklerini döndürür.
4. `__call__` yöntemi, girdi metnini (input text) alır ve gömme vektörünü (embedding vector) döndürür.

---

