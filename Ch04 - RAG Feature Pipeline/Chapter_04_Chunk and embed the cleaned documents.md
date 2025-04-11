# Benzer şekilde Dokümanları Temizlediğimiz Gibi, Parçalama ve Gömme Mantığını Her Veri Kategorisini Nasıl İşleyeceğini Bilen Bir Dispatcher'a Devrediyoruz

Belgelileri temizlediğimiz gibi, parçalama (`chunking`) ve gömme (`embedding`) mantığını her veri kategorisini (`data category`) nasıl işleyeceğini bilen bir `dispatcher`'a devrederiz. Parçalama (`chunking`) `dispatcher`'ının tek bir nesne yerine bir liste (`list`) döndürdüğünü unutmayın, bu mantıklıdır çünkü belge (`document`) birden çok parçaya (`chunk`) bölünmüştür. Bu bölümün "`The dispatcher layer`" bölümünde `dispatcher`'a daha yakından bakacağız.

```python
@step
def chunk_and_embed(
    cleaned_documents: Annotated[list, "cleaned_documents"],
) -> Annotated[list, "embedded_documents"]:
```

*   `@step`: Bu, bir fonksiyonun (`function`) ZenML'de bir adım (`step`) olarak işaretlendiğini belirtir.
*   `def chunk_and_embed`: `chunk_and_embed` adlı fonksiyonu tanımlar.
*   `cleaned_documents: Annotated[list, "cleaned_documents"]`: Fonksiyonun girdi parametresi (`input parameter`) olarak `cleaned_documents` adlı bir liste (`list`) alır ve bu liste temizlenmiş dokümanları (`cleaned documents`) içerir.
*   `-> Annotated[list, "embedded_documents"]`: Fonksiyonun çıktı tipi (`output type`) olarak gömülmüş dokümanları (`embedded documents`) içeren bir liste (`list`) döndürüleceğini belirtir.

```python
metadata = {
    "chunking": {},
    "embedding": {},
    "num_documents": len(cleaned_documents)
}
```

*   `metadata`: Bir sözlük (`dictionary`) tanımlar ve içine bazı anahtar-değer (`key-value`) çiftleri ekler.
*   `"chunking"`: Parçalama (`chunking`) işlemine ait meta verileri (`metadata`) saklamak için boş bir sözlük (`dictionary`) tanımlar.
*   `"embedding"`: Gömme (`embedding`) işlemine ait meta verileri (`metadata`) saklamak için boş bir sözlük (`dictionary`) tanımlar.
*   `"num_documents"`: Temizlenmiş dokümanların (`cleaned documents`) sayısını saklar.

```python
embedded_chunks = []
for document in cleaned_documents:
    chunks = ChunkingDispatcher.dispatch(document)
    metadata["chunking"] = _add_chunks_metadata(chunks, metadata["chunking"])
    for batched_chunks in utils.misc.batch(chunks, 10):
        batched_embedded_chunks = EmbeddingDispatcher.dispatch(batched_chunks)
        embedded_chunks.extend(batched_embedded_chunks)
```

*   `embedded_chunks = []`: Gömülmüş parçaları (`embedded chunks`) saklamak için boş bir liste (`list`) tanımlar.
*   `for document in cleaned_documents`: Temizlenmiş dokümanlar (`cleaned documents`) listesi üzerinde döngü (`loop`) kurar.
*   `chunks = ChunkingDispatcher.dispatch(document)`: Her bir dokümanı (`document`) parçalara (`chunk`) ayırmak için `ChunkingDispatcher`'ın `dispatch` metodunu çağırır.
*   `metadata["chunking"] = _add_chunks_metadata(chunks, metadata["chunking"])`: Parçalama (`chunking`) işlemine ait meta verileri (`metadata`) günceller.
*   `for batched_chunks in utils.misc.batch(chunks, 10)`: Parçaları (`chunks`) 10'arlık gruplara (`batch`) ayırarak döngü (`loop`) kurar.
*   `batched_embedded_chunks = EmbeddingDispatcher.dispatch(batched_chunks)`: Her bir grup parça (`batched chunks`) için gömme (`embedding`) işlemini gerçekleştirmek üzere `EmbeddingDispatcher`'ın `dispatch` metodunu çağırır.
*   `embedded_chunks.extend(batched_embedded_chunks)`: Gömülmüş parçaları (`embedded chunks`) `embedded_chunks` listesine ekler.

```python
metadata["embedding"] = _add_embeddings_metadata(embedded_chunks, metadata["embedding"])
metadata["num_chunks"] = len(embedded_chunks)
metadata["num_embedded_chunks"] = len(embedded_chunks)
step_context = get_step_context()
step_context.add_output_metadata(output_name="embedded_documents", metadata=metadata)
return embedded_chunks
```

*   `metadata["embedding"] = _add_embeddings_metadata(embedded_chunks, metadata["embedding"])`: Gömme (`embedding`) işlemine ait meta verileri (`metadata`) günceller.
*   `metadata["num_chunks"] = len(embedded_chunks)`: Parçaların (`chunks`) sayısını saklar.
*   `metadata["num_embedded_chunks"] = len(embedded_chunks)`: Gömülmüş parçaların (`embedded chunks`) sayısını saklar.
*   `step_context = get_step_context()`: Adım (`step`) bağlamını (`context`) alır.
*   `step_context.add_output_metadata(output_name="embedded_documents", metadata=metadata)`: Çıktı meta verilerini (`output metadata`) adım (`step`) bağlamına (`context`) ekler.
*   `return embedded_chunks`: Gömülmüş parçaları (`embedded chunks`) döndürür.

# Şekil 4.14'te Parçalama ve Gömme ZenML Adımının Meta Verilerini Görebilirsiniz

Örneğin, 76 dokümanı 2.373 parçaya (`chunk`) dönüştürdüğümüzü veya makaleleri parçalamak (`chunking`) için kullandığımız özellikler (`properties`) gibi `chunk_size` 500 ve `chunk_overlap` 50 olduğunu hızlıca anlayabilirsiniz.

# Şekil 4.15'te Gömme ve Parçalama ZenML Adımından Kalan Meta Veriler, Gömme Modelini ve Vektörleri Hesaplamak için Kullanılan Özelliklerini Detaylandırır

Makine öğrenimi (`Machine Learning`) sistemleri, üretimde (`production`) sürüklenme (`drift`) veya işlenmeyen kullanım durumları (`untreated use cases`) nedeniyle herhangi bir zamanda bozulabilir (`break`). Meta veri (`metadata`) bölümünü, alınan verileri (`ingested data`) izlemek için güçlü bir araç olarak kullanmak, hata ayıklama (`debugging`) günlerini, yani işletmeniz için on binlerce dolar veya daha fazlasını temsil eden tasarruflara yol açabilir.

---

