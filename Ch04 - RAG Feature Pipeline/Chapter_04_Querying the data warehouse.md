# İlk dikkat edilmesi gereken şey, bir adımın (@step ile dekore edilmiş) bir Python fonksiyonu olduğudur, tıpkı bir ZenML pipeline'un çalışmasına benzer.
Aşağıdaki fonksiyon, yazarların tam adlarının bir listesini girdi olarak alır ve aşağıdaki temel adımları gerçekleştirir: 
İlk ve son isimleri kullanarak bir UserDocument örneği (instance) almaya veya oluşturmaya çalışır ve bu örneği yazarlar listesine ekler. 
Eğer kullanıcı mevcut değilse, bir hata fırlatır. 
Kullanıcının ham verilerini veri ambarından (data warehouse) getirir ve belge listesini bu kullanıcı belgeleriyle genişletir. 
Sonunda, ZenML'de kaydedilen ve izlenen bir tanımlayıcı meta veri sözlüğü (descriptive metadata dictionary) hesaplar.

```python
# diğer importlar
from zenml import get_step_context, step

@step
def query_data_warehouse(
    author_full_names: list[str],
) -> Annotated[list, "raw_documents"]:
    documents = []
    authors = []
    for author_full_name in author_full_names:
        logger.info(f"Veri ambarından kullanıcı sorgulanıyor: {author_full_name}")
        first_name, last_name = utils.split_user_full_name(author_full_name)
        logger.info(f"İlk isim: {first_name}, Son isim: {last_name}")
        user = UserDocument.get_or_create(first_name=first_name, last_name=last_name)
        authors.append(user)
        results = fetch_all_data(user)  # fetch_all_data fonksiyonu çağrılıyor
        user_documents = [doc for query_result in results.values() for doc in query_result]
        documents.extend(user_documents)
    step_context = get_step_context()
    step_context.add_output_metadata(output_name="raw_documents", metadata=_get_metadata(documents))
    return documents
```

**Kod Açıklaması:**

1. `@step` dekore edicisi (decorator), bu fonksiyonun bir ZenML adımı olduğunu belirtir.
2. `query_data_warehouse` fonksiyonu, yazarların tam adlarının bir listesini girdi olarak alır.
3. Fonksiyon, her yazar için `UserDocument` örneği almaya veya oluşturmaya çalışır.
4. `fetch_all_data` fonksiyonunu çağırarak kullanıcının ham verilerini veri ambarından getirir.
5. `step_context.add_output_metadata` ile çıktı meta verilerini ZenML'de kaydeder.

# fetch_all_data Fonksiyonu
`fetch_all_data` fonksiyonu, her sorguyu farklı bir thread'de çalıştıran bir thread havuzu (thread pool) kullanır. 
Birden fazla veri kategorisi olduğu için, makaleler, gönderiler ve depolar için farklı sorgular yapmalıyız, çünkü bunlar farklı koleksiyonlarda depolanır. 
Her sorgu, veri ambarını çağırır, bu da ağ G/Ç'si (network I/O) ve veri ambarı gecikmesi (data warehouse latency) ile sınırlıdır, makinenin CPU'su ile değil. 
Bu nedenle, her sorguyu farklı bir thread'e taşıyarak, bunları paralel hale getirebiliriz.

```python
def fetch_all_data(user: UserDocument) -> dict[str, list[NoSQLBaseDocument]]:
    user_id = str(user.id)
    with ThreadPoolExecutor() as executor:
        future_to_query = {
            executor.submit(__fetch_articles, user_id): "articles",
            executor.submit(__fetch_posts, user_id): "posts",
            executor.submit(__fetch_repositories, user_id): "repositories",
        }
        results = {}
        for future in as_completed(future_to_query):
            query_name = future_to_query[future]
            try:
                results[query_name] = future.result()
            except Exception:
                logger.exception(f"'{query_name}' isteği başarısız oldu.")
                results[query_name] = []
    return results
```

**Kod Açıklaması:**

1. `fetch_all_data` fonksiyonu, bir `UserDocument` örneği alır.
2. `ThreadPoolExecutor` kullanarak bir thread havuzu oluşturur.
3. Üç farklı sorguyu (`__fetch_articles`, `__fetch_posts`, `__fetch_repositories`) farklı thread'lere taşır.
4. `as_completed` kullanarak, sorguların tamamlanmasını bekler.
5. Her sorgunun sonucunu `results` sözlüğüne ekler.

# _get_metadata Fonksiyonu
`_get_metadata` fonksiyonu, sorgulanan belgelerin ve yazarların listesini alır ve her veri kategorisine göre sayısını sayar.

```python
def _get_metadata(documents: list[Document]) -> dict:
    metadata = {"num_documents": len(documents)}
    for document in documents:
        collection = document.get_collection_name()
        if collection not in metadata:
            metadata[collection] = {}
        if "authors" not in metadata[collection]:
            metadata[collection]["authors"] = list()
        metadata[collection]["num_documents"] = metadata[collection].get("num_documents", 0) + 1
        metadata[collection]["authors"].append(document.author_full_name)
    for value in metadata.values():
        if isinstance(value, dict) and "authors" in value:
            value["authors"] = list(set(value["authors"]))
    return metadata
```

**Kod Açıklaması:**

1. `_get_metadata` fonksiyonu, belgelerin listesini alır.
2. `metadata` sözlüğünü oluşturur ve belge sayısını sayar.
3. Her belge için, koleksiyon adını alır ve `metadata` sözlüğüne ekler.
4. Yazarları `metadata` sözlüğüne ekler ve benzersiz yazarları hesaplar.

Bu meta veriler, ZenML dashboard'unda gösterilir ve yüklenen veriler hakkında hızlı bir şekilde istatistikler sağlar.

---

