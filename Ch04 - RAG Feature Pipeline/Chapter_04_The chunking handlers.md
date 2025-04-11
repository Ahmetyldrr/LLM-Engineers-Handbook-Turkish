# İlk olarak, ChunkingDataHandler() strateji handler'ını inceleyelim.
İlk olarak, ChunkingDataHandler() strateji handler'ını inceleyelim. Metadata sözlüğünü (dictionary) tek bir yapı içinde chunking için gerekli tüm özellikleri toplamak için bir özellik olarak ortaya koyduk. Bunu bu şekilde yapılandırarak, her şeyi ZenML'e loglamak ve chunking mantığımızı izlemek ve hata ayıklamak için kolayca yapabiliriz. Handler, temizlenmiş belgeleri (CleanedDocument) girdi olarak alır ve chunk varlıkları (Chunk entities) döndürür. Tüm handler'lar GitHub'da https://github.com/PacktPublishing/LLM-Engineering/tree/main/llm_engineering/application/preprocessing adresinde bulunabilir.

```python
# Diğer içe aktarmalar (Other imports).
from typing import Generic, TypeVar

CleanedDocumentT = TypeVar("CleanedDocumentT", bound=CleanedDocument)
ChunkT = TypeVar("ChunkT", bound=Chunk)

class ChunkingDataHandler(ABC, Generic[CleanedDocumentT, ChunkT]):
    @property
    def metadata(self) -> dict:
        return {
            "chunk_size": 500,
            "chunk_overlap": 50,
        }

    @abstractmethod
    def chunk(self, data_model: CleanedDocumentT) -> list[ChunkT]:
        pass
```

**Kod Açıklaması:**

*   `CleanedDocumentT` ve `ChunkT` değişken türleri (TypeVar) tanımlanır. Bunlar sırasıyla `CleanedDocument` ve `Chunk` sınıflarından türetilen türlerdir.
*   `ChunkingDataHandler` sınıfı, `ABC` (Soyut Base Class) ve `Generic` sınıflarından miras alır. Bu, onun bir soyut sınıf ve jenerik bir tür olmasını sağlar.
*   `metadata` özelliği, chunking için gerekli özellikleri içeren bir sözlük döndürür. Bu özellik, alt sınıflar tarafından geçersiz kılınabilir (override).
*   `chunk` metodu, soyut bir metoddur ve alt sınıflar tarafından uygulanmalıdır. Temizlenmiş belgeleri (CleanedDocument) girdi olarak alır ve chunk varlıkları (Chunk entities) döndürür.

# ArticleChunkingHandler() sınıfının uygulanmasını anlayalım.
ArticleChunkingHandler() sınıfının uygulanmasını anlayalım. İlk adım, metadata özelliğini geçersiz kılmak (override) ve chunking mantığının gerektirdiği özellik türlerini özelleştirmektir. Örneğin, makalelerle çalışırken, chunk'ın minimum ve maksimum uzunluğuyla ilgileniriz. Handler'ın `chunk()` metodu, temizlenmiş makale belgelerini (CleanedArticleDocument) girdi olarak alır ve makale chunk varlıkları (ArticleChunk entities) listesi döndürür. `chunk_text()` fonksiyonunu kullanarak temizlenmiş içeriği chunk'lara böler. Chunking fonksiyonu, `min_length` ve `max_length` metadata alanlarına göre özelleştirilir. `chunk_id`, chunk'ın içeriğinin MD5 hash'i olarak hesaplanır. Böylece, iki chunk'ın içeriği tamamen aynıysa, aynı kimliğe sahip olacak ve kolayca kopyalarını kaldırabiliriz. Son olarak, chunk varlıkları listesi oluşturulur ve döndürülür.

```python
class ArticleChunkingHandler(ChunkingDataHandler):
    @property
    def metadata(self) -> dict:
        return {
            "min_length": 1000,
            "max_length": 1000,
        }

    def chunk(self, data_model: CleanedArticleDocument) -> list[ArticleChunk]:
        data_models_list = []
        cleaned_content = data_model.content
        chunks = chunk_article(
            cleaned_content, min_length=self.metadata["min_length"], max_length=self.metadata["max_length"]
        )
        for chunk in chunks:
            chunk_id = hashlib.md5(chunk.encode()).hexdigest()
            model = ArticleChunk(
                id=UUID(chunk_id, version=4),
                content=chunk,
                platform=data_model.platform,
                link=data_model.link,
                document_id=data_model.id,
                author_id=data_model.author_id,
                author_full_name=data_model.author_full_name,
                metadata=self.metadata,
            )
            data_models_list.append(model)
        return data_models_list
```

**Kod Açıklaması:**

*   `ArticleChunkingHandler` sınıfı, `ChunkingDataHandler` sınıfından miras alır.
*   `metadata` özelliği, `min_length` ve `max_length` özelliklerini içeren bir sözlük döndürür.
*   `chunk` metodu, temizlenmiş makale belgelerini (CleanedArticleDocument) girdi olarak alır ve makale chunk varlıkları (ArticleChunk entities) listesi döndürür.
*   `chunk_article` fonksiyonu, temizlenmiş içeriği chunk'lara böler.
*   Her chunk için, `chunk_id` MD5 hash'i olarak hesaplanır ve bir `ArticleChunk` nesnesi oluşturulur.

# chunk_article() fonksiyonunu inceleyelim.
`chunk_article()` fonksiyonunu inceleyelim. Bu fonksiyon esas olarak iki şey yapar: Bir regex kullanarak, verilen metin içinde nokta, soru işareti veya ünlem işareti ardından boşluk gelen tüm cümleleri bulur. Ancak, noktalama işaretinin bir kısaltma veya kısaltılmış isim içinde olduğu durumlarda bölmez (örneğin "e.g." veya "Dr."). Cümleleri, `max_length` sınırına kadar tek bir chunk içinde gruplar. Maksimum boyuta ulaşıldığında ve chunk boyutu minimum izin verilen değerden büyük olduğunda, nihai liste içinde döndürülecek olan chunk eklenir.

```python
def chunk_article(text: str, min_length: int, max_length: int) -> list[str]:
    sentences = re.split(r"(?<!\w\.\w.)(?<![A-Z][a-z]\.)(?<=\.|\?|\!)\s", text)
    extracts = []
    current_chunk = ""
    for sentence in sentences:
        sentence = sentence.strip()
        if not sentence:
            continue
        if len(current_chunk) + len(sentence) <= max_length:
            current_chunk += sentence + " "
        else:
            if len(current_chunk) >= min_length:
                extracts.append(current_chunk.strip())
            current_chunk = sentence + " "
    if len(current_chunk) >= min_length:
        extracts.append(current_chunk.strip())
    return extracts
```

**Kod Açıklaması:**

*   `chunk_article` fonksiyonu, metni cümlelere böler ve `min_length` ve `max_length` sınırlarına göre chunk'lara ayırır.
*   Cümleler, `max_length` sınırına kadar tek bir chunk içinde gruplanır.
*   Maksimum boyuta ulaşıldığında ve chunk boyutu minimum izin verilen değerden büyük olduğunda, chunk nihai liste içinde döndürülecek olan chunk eklenir.

# chunk_text() fonksiyonunu inceleyelim.
`chunk_text()` fonksiyonunu inceleyelim. Bu fonksiyon, iki aşamalı bir işlemdir: LangChain'den `RecursiveCharacterTextSplitter()` kullanarak metni verilen bir ayırıcıya (separator) veya chunk boyutuna göre böler. Ayırıcı kullanarak, önce verilen metin içinde paragrafları bulmaya çalışır, ancak paragraflar yoksa veya çok uzunsa, verilen chunk boyutunda keser. Embedding modelinin maksimum girdi uzunluğunu aşmadığından emin olmak isteriz. Bu nedenle, yukarıda oluşturulan tüm chunk'ları `SenteceTransformersTokenTextSplitter()` içine geçiririz, bu da modelin maksimum girdi uzunluğunu dikkate alır. Bu noktada, chunk_overlap mantığını da uygularız, çünkü chunk'ın yeterince küçük olduğunu doğruladıktan sonra bunu yapmak isteriz.

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter, SentenceTransformersTokenTextSplitter
from llm_engineering.application.networks import EmbeddingModelSingleton

def chunk_text(text: str, chunk_size: int = 500, chunk_overlap: int = 50) -> list[str]:
    character_splitter = RecursiveCharacterTextSplitter(separators=["\n\n"], chunk_size=chunk_size, chunk_overlap=0)
    text_split_by_characters = character_splitter.split_text(text)
    token_splitter = SentenceTransformersTokenTextSplitter(
        chunk_overlap=chunk_overlap,
        tokens_per_chunk=embedding_model.max_input_length,
        model_name=embedding_model.model_id,
    )
    chunks_by_tokens = []
    for section in text_split_by_characters:
        chunks_by_tokens.extend(token_splitter.split_text(section))
    return chunks_by_tokens
```

**Kod Açıklaması:**

*   `chunk_text` fonksiyonu, metni chunk'lara böler.
*   `RecursiveCharacterTextSplitter` kullanarak metni verilen bir ayırıcıya veya chunk boyutuna göre böler.
*   `SenteceTransformersTokenTextSplitter` kullanarak chunk'ları embedding modelinin maksimum girdi uzunluğuna göre ayarlar.
*   Chunk_overlap mantığını uygular.

---

