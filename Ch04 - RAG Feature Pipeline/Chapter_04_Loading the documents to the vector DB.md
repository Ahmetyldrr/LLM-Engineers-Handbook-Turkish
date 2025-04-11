#Vektör Veritabanına Yükleme (Loading to Vector DB)

Her bir makale, gönderi veya kod deposu vektör veritabanı (vector DB) içinde farklı bir koleksiyonda (collection) bulunduğu için, tüm belgeleri veri kategorilerine (data category) göre gruplandırmamız gerekir. Ardından, her bir grubu Qdrant vektör veritabanına (Qdrant vector DB) toplu olarak yüklüyoruz:

```python
@step
def load_to_vector_db(
    documents: Annotated[list, "documents"],
) -> None:
    logger.info(f"Loading {len(documents)} documents into the vector database.")
    grouped_documents = VectorBaseDocument.group_by_class(documents)
    for document_class, documents in grouped_documents.items():
        logger.info(f"Loading documents into {document_class.get_collection_name()} ")
        for documents_batch in utils.misc.batch(documents, size=4):
            try:
                document_class.bulk_insert(documents_batch)
            except Exception:
                return False
    return True
```

Kod Açıklaması:

1. `@step`: Bu, bir dekoratördür (decorator) ve fonksiyonun bir adım (step) olarak işaretlendiğini belirtir.
2. `def load_to_vector_db`: `load_to_vector_db` adlı fonksiyonu tanımlar. Bu fonksiyon, vektör veritabanına belge yüklemek için kullanılır.
3. `documents: Annotated[list, "documents"]`: Fonksiyonun `documents` adlı parametresini tanımlar. Bu parametre, bir liste (list) tipindedir ve "documents" etiketiyle açıklanmıştır.
4. `-> None`: Fonksiyonun geri dönüş tipini belirtir. Bu fonksiyon, hiçbir şey döndürmez (`None`).
5. `logger.info(f"Loading {len(documents)} documents into the vector database.")`: Fonksiyon, vektör veritabanına yüklenen belge sayısını loglar (logger.info). `len(documents)` ifadesi, `documents` listesinin eleman sayısını verir.
6. `grouped_documents = VectorBaseDocument.group_by_class(documents)`: Belgeleri veri kategorilerine göre gruplandırır. `VectorBaseDocument.group_by_class` metodu, belgeleri sınıflarına (class) göre gruplandırır.
7. `for document_class, documents in grouped_documents.items():`: Gruplandırılmış belgeler üzerinde döngü (loop) oluşturur. Her bir döngüde, `document_class` değişkeni, belge sınıfını (document class) ve `documents` değişkeni, o sınıfa ait belgeleri temsil eder.
8. `logger.info(f"Loading documents into {document_class.get_collection_name()} ")`: Her bir belge sınıfı için, o sınıfa ait belgelerin yüklendiği koleksiyonun (collection) adını loglar.
9. `for documents_batch in utils.misc.batch(documents, size=4):`: Belgeleri 4'erli gruplar halinde (batch) ayırır. `utils.misc.batch` fonksiyonu, belgeleri belirtilen boyutta (size) gruplara ayırır.
10. `try:`: Hata yakalama (try-except) bloğunu başlatır.
11. `document_class.bulk_insert(documents_batch)`: Her bir belge grubunu (batch), ilgili koleksiyona toplu olarak (bulk) ekler.
12. `except Exception:`: Hata yakalama bloğunda, herhangi bir hata oluşursa, `return False` ifadesi çalışır ve fonksiyon `False` döndürür.
13. `return True`: Fonksiyon, başarılı bir şekilde çalıştıysa, `True` döndürür.

İngilizce Teknik Terimlerin Türkçeleri ve Parantez İçinde İngilizce Karşılıkları:

* Vektör Veritabanı (Vector DB)
* Koleksiyon (Collection)
* Veri Kategorisi (Data Category)
* Belgeler (Documents)
* Sınıf (Class)
* Toplu Ekleme (Bulk Insert)
* Gruplandırma (Grouping)
* Döngü (Loop)
* Hata Yakalama (Try-Except)
* Loglama (Logging)

---

