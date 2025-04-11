#digital_data_etl Pipeline'ının Uygulanması
Aşağıdaki kod parçasında, ZenML digital_data_etl pipeline'unun uygulanmasını görebiliriz. Bu pipeline, kullanıcının tam adını ve o kullanıcıya ait (bu linklerden çıkarılan içeriğin yazarı olarak kabul edilir) taranacak link listesini girdi olarak alır. Fonksiyon içinde iki adım çağırıyoruz. İlk adımda, kullanıcıyı veritabanında tam adına göre arıyoruz. Ardından, tüm linkleri döngüye sokarak her birini bağımsız olarak tarıyoruz. Pipeline'ın uygulanması pipelines/digital_data_etl.py adresindeki depomuzda mevcuttur.

```python
from zenml import pipeline
from steps.etl import crawl_links, get_or_create_user

@pipeline
def digital_data_etl(user_full_name: str, links: list[str]) -> str:
    user = get_or_create_user(user_full_name)
    last_step = crawl_links(user=user, links=links)
    return last_step.invocation_id
```

**Kod Açıklaması:**

1. `from zenml import pipeline`: ZenML kütüphanesinden `pipeline` dekoratörünü içe aktarır. Bu, bir fonksiyonu ZenML pipeline'ı olarak tanımlar.
2. `from steps.etl import crawl_links, get_or_create_user`: `steps.etl` modülünden `crawl_links` ve `get_or_create_user` fonksiyonlarını içe aktarır. Bu fonksiyonlar pipeline içinde kullanılan adımları temsil eder.
3. `@pipeline`: Fonksiyonu ZenML pipeline'ı olarak tanımlar.
4. `def digital_data_etl(user_full_name: str, links: list[str]) -> str:`: `digital_data_etl` fonksiyonunu tanımlar. Bu fonksiyon, `user_full_name` ve `links` parametrelerini alır ve bir dize (`str`) döndürür.
5. `user = get_or_create_user(user_full_name)`: `get_or_create_user` adımını çağırarak kullanıcıyı veritabanında arar veya oluşturur.
6. `last_step = crawl_links(user=user, links=links)`: `crawl_links` adımını çağırarak linkleri tarar.
7. `return last_step.invocation_id`: Son adımın (`last_step`) `invocation_id` değerini döndürür.

# get_or_create_user ZenML Adımı
`get_or_create_user` ZenML adımını inceleyeceğiz. İlk olarak, gerekli modülleri ve fonksiyonları içe aktarıyoruz.

```python
from loguru import logger
from typing_extensions import Annotated
from zenml import get_step_context, step
from llm_engineering.application import utils
from llm_engineering.domain.documents import UserDocument
```

**Kod Açıklaması:**

1. `from loguru import logger`: Loguru kütüphanesinden `logger` nesnesini içe aktarır. Bu, loglama işlemleri için kullanılır.
2. `from typing_extensions import Annotated`: Typing Extensions kütüphanesinden `Annotated` sınıfını içe aktarır. Bu, tür açıklamaları için kullanılır.
3. `from zenml import get_step_context, step`: ZenML kütüphanesinden `get_step_context` ve `step` dekoratörünü içe aktarır. `get_step_context` geçerli adım bağlamını alır, `step` ise bir fonksiyonu ZenML adımı olarak tanımlar.
4. `from llm_engineering.application import utils`: `llm_engineering.application` modülünden `utils` modülünü içe aktarır. Bu modül, çeşitli yardımcı fonksiyonlar içerir.
5. `from llm_engineering.domain.documents import UserDocument`: `llm_engineering.domain.documents` modülünden `UserDocument` sınıfını içe aktarır. Bu sınıf, kullanıcı belgelerini temsil eder.

```python
@step
def get_or_create_user(user_full_name: str) -> Annotated[UserDocument, "user"]:
    logger.info(f"Getting or creating user: {user_full_name}")
    first_name, last_name = utils.split_user_full_name(user_full_name)
    user = UserDocument.get_or_create(first_name=first_name, last_name=last_name)
    step_context = get_step_context()
    step_context.add_output_metadata(output_name="user", metadata=_get_metadata(user_full_name, user))
    return user
```

**Kod Açıklaması:**

1. `@step`: Fonksiyonu ZenML adımı olarak tanımlar.
2. `def get_or_create_user(user_full_name: str) -> Annotated[UserDocument, "user"]:`: `get_or_create_user` fonksiyonunu tanımlar. Bu fonksiyon, `user_full_name` parametresini alır ve bir `UserDocument` nesnesi döndürür.
3. `logger.info(f"Getting or creating user: {user_full_name}")`: Loglama işlemi yapar.
4. `first_name, last_name = utils.split_user_full_name(user_full_name)`: `utils.split_user_full_name` fonksiyonunu kullanarak tam adı ad ve soyad olarak ayırır.
5. `user = UserDocument.get_or_create(first_name=first_name, last_name=last_name)`: `UserDocument.get_or_create` fonksiyonunu kullanarak kullanıcıyı veritabanında arar veya oluşturur.
6. `step_context = get_step_context()`: Geçerli adım bağlamını alır.
7. `step_context.add_output_metadata(output_name="user", metadata=_get_metadata(user_full_name, user))`: Adım çıktısına meta veri ekler.
8. `return user`: Kullanıcı nesnesini döndürür.

```python
def _get_metadata(user_full_name: str, user: UserDocument) -> dict:
    return {
        "query": {
            "user_full_name": user_full_name,
        },
        "retrieved": {
            "user_id": str(user.id),
            "first_name": user.first_name,
            "last_name": user.last_name,
        },
    }
```

**Kod Açıklaması:**

1. `def _get_metadata(user_full_name: str, user: UserDocument) -> dict:`: `_get_metadata` fonksiyonunu tanımlar. Bu fonksiyon, `user_full_name` ve `user` parametrelerini alır ve bir sözlük (`dict`) döndürür.
2. `return { ... }`: Meta verileri içeren bir sözlük döndürür.

# crawl_links ZenML Adımı
`crawl_links` ZenML adımını inceleyeceğiz. İlk olarak, gerekli modülleri ve kütüphaneleri içe aktarıyoruz.

```python
from urllib.parse import urlparse
from loguru import logger
from tqdm import tqdm
from typing_extensions import Annotated
from zenml import get_step_context, step
from llm_engineering.application.crawlers.dispatcher import CrawlerDispatcher
from llm_engineering.domain.documents import UserDocument
```

**Kod Açıklaması:**

1. `from urllib.parse import urlparse`: Urllib kütüphanesinden `urlparse` fonksiyonunu içe aktarır. Bu, URL ayrıştırmak için kullanılır.
2. `from loguru import logger`: Loguru kütüphanesinden `logger` nesnesini içe aktarır. Bu, loglama işlemleri için kullanılır.
3. `from tqdm import tqdm`: TQDM kütüphanesinden `tqdm` fonksiyonunu içe aktarır. Bu, ilerleme çubuğu oluşturmak için kullanılır.
4. `from typing_extensions import Annotated`: Typing Extensions kütüphanesinden `Annotated` sınıfını içe aktarır. Bu, tür açıklamaları için kullanılır.
5. `from zenml import get_step_context, step`: ZenML kütüphanesinden `get_step_context` ve `step` dekoratörünü içe aktarır. `get_step_context` geçerli adım bağlamını alır, `step` ise bir fonksiyonu ZenML adımı olarak tanımlar.
6. `from llm_engineering.application.crawlers.dispatcher import CrawlerDispatcher`: `llm_engineering.application.crawlers.dispatcher` modülünden `CrawlerDispatcher` sınıfını içe aktarır. Bu sınıf, tarayıcıları yönetmek için kullanılır.
7. `from llm_engineering.domain.documents import UserDocument`: `llm_engineering.domain.documents` modülünden `UserDocument` sınıfını içe aktarır. Bu sınıf, kullanıcı belgelerini temsil eder.

```python
@step
def crawl_links(user: UserDocument, links: list[str]) -> Annotated[list[str], "crawled_links"]:
    dispatcher = CrawlerDispatcher.build().register_linkedin().register_medium().register_github()
    logger.info(f"Starting to crawl {len(links)} link(s).")
    metadata = {}
    successful_crawls = 0
    for link in tqdm(links):
        successful_crawl, crawled_domain = _crawl_link(dispatcher, link, user)
        successful_crawls += successful_crawl
        metadata = _add_to_metadata(metadata, crawled_domain, successful_crawl)
    step_context = get_step_context()
    step_context.add_output_metadata(output_name="crawled_links", metadata=metadata)
    logger.info(f"Successfully crawled {successful_crawls} / {len(links)} links.")
    return links
```

**Kod Açıklaması:**

1. `@step`: Fonksiyonu ZenML adımı olarak tanımlar.
2. `def crawl_links(user: UserDocument, links: list[str]) -> Annotated[list[str], "crawled_links"]:`: `crawl_links` fonksiyonunu tanımlar. Bu fonksiyon, `user` ve `links` parametrelerini alır ve bir link listesi döndürür.
3. `dispatcher = CrawlerDispatcher.build().register_linkedin().register_medium().register_github()`: `CrawlerDispatcher` sınıfını kullanarak tarayıcıları kaydeder.
4. `logger.info(f"Starting to crawl {len(links)} link(s).")`: Loglama işlemi yapar.
5. `metadata = {}`: Meta verileri saklamak için bir sözlük oluşturur.
6. `successful_crawls = 0`: Başarılı tarama sayısını saklamak için bir sayaç oluşturur.
7. `for link in tqdm(links):`: Linkleri döngüye sokar ve her birini tarar.
8. `step_context = get_step_context()`: Geçerli adım bağlamını alır.
9. `step_context.add_output_metadata(output_name="crawled_links", metadata=metadata)`: Adım çıktısına meta veri ekler.
10. `logger.info(f"Successfully crawled {successful_crawls} / {len(links)} links.")`: Loglama işlemi yapar.
11. `return links`: Link listesini döndürür.

```python
def _crawl_link(dispatcher: CrawlerDispatcher, link: str, user: UserDocument) -> tuple[bool, str]:
    crawler = dispatcher.get_crawler(link)
    crawler_domain = urlparse(link).netloc
    try:
        crawler.extract(link=link, user=user)
        return (True, crawler_domain)
    except Exception as e:
        logger.error(f"An error occurred while crawling: {e!s}")
        return (False, crawler_domain)
```

**Kod Açıklaması:**

1. `def _crawl_link(dispatcher: CrawlerDispatcher, link: str, user: UserDocument) -> tuple[bool, str]:`: `_crawl_link` fonksiyonunu tanımlar. Bu fonksiyon, `dispatcher`, `link` ve `user` parametrelerini alır ve bir tuple (`bool`, `str`) döndürür.
2. `crawler = dispatcher.get_crawler(link)`: `dispatcher.get_crawler` fonksiyonunu kullanarak tarayıcı alır.
3. `crawler_domain = urlparse(link).netloc`: Linki ayrıştırmak için `urlparse` fonksiyonunu kullanır.
4. `try: ... except Exception as e:`: Hata yakalama bloğu oluşturur.
5. `crawler.extract(link=link, user=user)`: Tarayıcının `extract` fonksiyonunu çağırarak linki tarar.
6. `return (True, crawler_domain)`: Tarama başarılı ise `True` ve taranan domaini döndürür.
7. `logger.error(f"An error occurred while crawling: {e!s}")`: Hata loglar.
8. `return (False, crawler_domain)`: Tarama başarısız ise `False` ve taranan domaini döndürür.

```python
def _add_to_metadata(metadata: dict, domain: str, successful_crawl: bool) -> dict:
    if domain not in metadata:
        metadata[domain] = {}
    metadata[domain]["successful"] = metadata.get(domain, {}).get("successful", 0) + successful_crawl
    metadata[domain]["total"] = metadata.get(domain, {}).get("total", 0) + 1
    return metadata
```

**Kod Açıklaması:**

1. `def _add_to_metadata(metadata: dict, domain: str, successful_crawl: bool) -> dict:`: `_add_to_metadata` fonksiyonunu tanımlar. Bu fonksiyon, `metadata`, `domain` ve `successful_crawl` parametrelerini alır ve güncellenmiş meta verileri döndürür.
2. `if domain not in metadata:`: Domain meta verilerde yoksa yeni bir giriş oluşturur.
3. `metadata[domain]["successful"] = ...`: Başarılı tarama sayısını günceller.
4. `metadata[domain]["total"] = ...`: Toplam tarama sayısını günceller.
5. `return metadata`: Güncellenmiş meta verileri döndürür.

---

