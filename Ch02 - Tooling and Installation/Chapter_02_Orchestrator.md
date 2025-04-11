# Bir Orchestrator (Orkestra) Nedir ve ZenML Nasıl Çalışır?

Bir orkestra, tüm makine öğrenimi (ML) pipeline'larını otomatikleştiren, zamanlayan ve koordine eden bir sistemdir. Her bir pipeline'ın - veri alımı, ön işleme, model eğitimi ve dağıtımı gibi - doğru sırayla çalışmasını sağlar ve bağımlılıkları verimli bir şekilde yönetir. Bu süreçleri yöneterek, bir orkestra kaynak kullanımını optimize eder, hataları zarif bir şekilde işler ve ölçeklenebilirliği artırarak karmaşık ML pipeline'larını daha güvenilir ve yönetilebilir hale getirir.

# ZenML Nasıl Çalışır?

ZenML, pipeline'lar ve adımlarla (step) çalışır. Bir pipeline, birden fazla adım içeren yüksek seviyeli bir nesnedir. Bir fonksiyon, `@pipeline` dekoratörü ile süslendiğinde bir ZenML pipeline'ı haline gelir ve bir adım, `@step` dekoratörü ile süslendiğinde bir ZenML adımı olur. Bu, orkestra kullanırken standart bir desendir: yüksek seviyeli bir fonksiyonunuz (genellikle pipeline olarak adlandırılır) vardır ve bu, birden fazla birimi/adımı/görevi çağırır.

## ZenML Pipeline'ı Tanımlama

Aşağıdaki kod parçasında, LLM Twin projesi için uygulanan ML pipeline'larından biri ile bir ZenML pipeline'ı nasıl uygulanabileceğini görelim:
```python
from zenml import pipeline
from steps.etl import crawl_links, get_or_create_user

@pipeline
def digital_data_etl(user_full_name: str, links: list[str]) -> None:
    user = get_or_create_user(user_full_name)
    crawl_links(user=user, links=links)
```
Bu pipeline, veritabanından tam adına göre bir kullanıcı sorgulayan ve bu kullanıcı altında sağlanan tüm bağlantıları tarayan bir ZenML pipeline'ıdır.

### Kod Açıklaması

1. `from zenml import pipeline`: ZenML kütüphanesinden `pipeline` dekoratörünü içe aktarır.
2. `from steps.etl import crawl_links, get_or_create_user`: `steps.etl` modülünden `crawl_links` ve `get_or_create_user` fonksiyonlarını içe aktarır.
3. `@pipeline`: Bu dekoratör, `digital_data_etl` fonksiyonunu bir ZenML pipeline'ı haline getirir.
4. `def digital_data_etl(user_full_name: str, links: list[str]) -> None:`: `digital_data_etl` pipeline'ını tanımlar. Bu pipeline, `user_full_name` ve `links` parametrelerini alır ve hiçbir değer döndürmez (`-> None`).
5. `user = get_or_create_user(user_full_name)`: `get_or_create_user` adımını çağırarak `user_full_name` parametresi ile bir kullanıcı alır veya oluşturur.
6. `crawl_links(user=user, links=links)`: `crawl_links` adımını çağırarak `user` ve `links` parametreleri ile bağlantıları tarar.

## Pipeline'ı Çalıştırma

Pipeline'ı çalıştırmak için aşağıdaki CLI komutunu kullanabilirsiniz:
```bash
poetry poe run-digital-data-etl
```
Pipeline'ın çalışmasını görselleştirmek için ZenML dashboard'una gidin (`http://127.0.0.1:8237/`). Sol panelde, Pipelines sekmesine tıklayın ve ardından `digital_data_etl` pipeline'ına tıklayın (Şekil 2.2).

### ZenML Dashboard

ZenML dashboard'unda, pipeline'ın önceki ve mevcut çalıştırmalarını görebilirsiniz (Şekil 2.3). Hangi çalıştırmaların başarılı, başarısız veya hala çalışmakta olduğunu görebilirsiniz. Ayrıca, pipeline'ı çalıştırmak için kullanılan yığını (stack) görebilirsiniz.

## Adım Tanımlama

Aşağıdaki kod parçasında, `get_or_create_user` adımını nasıl tanımlayabileceğimizi görelim:
```python
from loguru import logger
from typing_extensions import Annotated
from zenml import get_step_context, step
from llm_engineering.application import utils
from llm_engineering.domain.documents import UserDocument

@step
def get_or_create_user(user_full_name: str) -> Annotated[UserDocument, "user"]:
    logger.info(f"Getting or creating user: {user_full_name}")
    first_name, last_name = utils.split_user_full_name(user_full_name)
    user = UserDocument.get_or_create(first_name=first_name, last_name=last_name)
    return user
```
Bu adım, bir kullanıcı alır veya oluşturur ve `UserDocument` nesnesini döndürür.

### Kod Açıklaması

1. `from loguru import logger`: Loguru kütüphanesinden `logger` nesnesini içe aktarır.
2. `from typing_extensions import Annotated`: Typing Extensions kütüphanesinden `Annotated` türünü içe aktarır.
3. `from zenml import get_step_context, step`: ZenML kütüphanesinden `get_step_context` ve `step` dekoratörünü içe aktarır.
4. `@step`: Bu dekoratör, `get_or_create_user` fonksiyonunu bir ZenML adımı haline getirir.
5. `def get_or_create_user(user_full_name: str) -> Annotated[UserDocument, "user"]:`: `get_or_create_user` adımını tanımlar. Bu adım, `user_full_name` parametresini alır ve `UserDocument` nesnesini döndürür.
6. `logger.info(f"Getting or creating user: {user_full_name}")`: Kullanıcı alma veya oluşturma işlemini loglar.
7. `first_name, last_name = utils.split_user_full_name(user_full_name)`: `user_full_name` parametresini `first_name` ve `last_name` olarak ayırır.
8. `user = UserDocument.get_or_create(first_name=first_name, last_name=last_name)`: `first_name` ve `last_name` parametreleri ile bir kullanıcı alır veya oluşturur.
9. `return user`: `UserDocument` nesnesini döndürür.

## Sonuç

ZenML, makine öğrenimi pipeline'larını otomatikleştiren, zamanlayan ve koordine eden bir orkestrasyon aracıdır. Pipeline'lar ve adımlar kullanarak, karmaşık ML pipeline'larını daha güvenilir ve yönetilebilir hale getirebilirsiniz. ZenML dashboard'unda, pipeline'ın çalıştırmalarını görebilir ve adım tanımlama ve pipeline oluşturma işlemlerini gerçekleştirebilirsiniz.

---

