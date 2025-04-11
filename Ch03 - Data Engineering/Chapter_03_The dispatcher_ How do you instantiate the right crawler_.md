# CrawlerDispatcher Sınıfının Uygulanması
Tırmanma (crawling) mantığımızın giriş noktası CrawlerDispatcher sınıfıdır. Şekil 3.4'te gösterildiği gibi, dispatcher sağlanan bağlantılar (links) ve tarayıcılar (crawlers) arasında ara katman olarak görev yapar. Her bir URL ile hangi tarayıcının ilişkilendirileceğini bilir. CrawlerDispatcher sınıfı, her bir bağlantının alan adını (domain) çıkarmayı ve o siteden veri toplayan uygun tarayıcıyı başlatmayı bilir. Örneğin, bir makaleye bağlantı sağlarken https://medium.com alan adını tespit ederse, o özel platformu taramak için kullanılan MediumCrawler'ın bir örneğini oluşturacaktır. Bunu akılda tutarak, CrawlerDispatcher sınıfının uygulanmasını inceleyelim. Tüm tarama mantığı GitHub deposunda llm_engineering/application/crawlers adresinde mevcuttur.

Şekil 3.4: Sağlanan bağlantılar, CrawlerDispatcher ve tarayıcılar arasındaki ilişki

URL işleme (URL handling) ve regex için gerekli Python modüllerini içe aktararak ve tarayıcı sınıflarımızı içe aktararak başlıyoruz:
```python
import re
from urllib.parse import urlparse
from loguru import logger
from .base import BaseCrawler
from .custom_article import CustomArticleCrawler
from .github import GithubCrawler
from .linkedin import LinkedInCrawler
from .medium import MediumCrawler
```
CrawlerDispatcher sınıfı, verilen URL'lere ve alanlarına göre uygun tarayıcı örneklerini yönetmek ve göndermek için tanımlanmıştır. Yapıcı (constructor), kayıtlı tarayıcıları depolamak için bir kayıt defteri (registry) başlatır.
```python
class CrawlerDispatcher:
    def __init__(self) -> None:
        self._crawlers = {}
```
Oluşturucu (builder) yaratıcı desenini (creational pattern) kullanarak dispatcher'ı örneklemek ve yapılandırmak için bir build() sınıf yöntemi tanımlarız:
```python
@classmethod
def build(cls) -> "CrawlerDispatcher":
    dispatcher = cls()
    return dispatcher
```
Dispatcher, Medium, LinkedIn ve GitHub gibi belirli platformlar için tarayıcıları kaydetme yöntemlerini içerir. Bu yöntemler, her bir tarayıcıyı kayıt defterine eklemek için genel bir register() yöntemi kullanır. Self döndürerek, oluşturucu yaratıcı desenini izleriz (oluşturucu deseni hakkında daha fazla bilgi: https://refactoring.guru/design-patterns/builder). Aşağıdaki gibi dispatcher'ı örneklerken birden fazla register_*() yöntemini zincirleyebiliriz:
```python
CrawlerDispatcher.build().register_linkedin().register_medium()
```
```python
def register_medium(self) -> "CrawlerDispatcher":
    self.register("https://medium.com", MediumCrawler)
    return self

def register_linkedin(self) -> "CrawlerDispatcher":
    self.register("https://linkedin.com", LinkedInCrawler)
    return self

def register_github(self) -> "CrawlerDispatcher":
    self.register("https://github.com", GithubCrawler)
    return self
```
Genel register() yöntemi, her bir alan adını (domain) normalize eder ve dispatcher'ın self._crawlers kayıt defterine anahtar olarak eklenmeden önce formatının tutarlı olmasını sağlar. Bu kritik bir adımdır, çünkü gelecekteki bağlantıları bir tarayıcı ile eşleştirmek için sözlük anahtarını kullanacağız:
```python
def register(self, domain: str, crawler: type[BaseCrawler]) -> None:
    parsed_domain = urlparse(domain)
    domain = parsed_domain.netloc
    self._crawlers[r"https://(www\.)?{}/*".format(re.escape(domain))] = crawler
```
Son olarak, get_crawler() yöntemi, verilen bir URL için uygun tarayıcıyı, kayıtlı alan adlarıyla eşleştirerek belirler. Bir eşleşme bulunamazsa, bir uyarı kaydeder ve varsayılan olarak CustomArticleCrawler'ı kullanır:
```python
def get_crawler(self, url: str) -> BaseCrawler:
    for pattern, crawler in self._crawlers.items():
        if re.match(pattern, url):
            return crawler()
    else:
        logger.warning(f"No crawler found for {url}. Defaulting to CustomArticleCrawler.")
        return CustomArticleCrawler()
```
Veri toplama işlem hattının nasıl çalıştığını anlamanın bir sonraki adımı, her bir tarayıcıyı ayrı ayrı analiz etmektir.

**Kod Açıklaması**

1. `import re`: Düzenli ifadeler (regular expressions) için Python'un `re` modülünü içe aktarır.
2. `from urllib.parse import urlparse`: URL'leri ayrıştırmak (parse) için `urlparse` işlevini içe aktarır.
3. `from loguru import logger`: Loglama (logging) için `logger` nesnesini içe aktarır.
4. `from .base import BaseCrawler`: Temel tarayıcı sınıfını (`BaseCrawler`) içe aktarır.
5. `class CrawlerDispatcher:`: CrawlerDispatcher sınıfını tanımlar.
6. `def __init__(self) -> None:`: Yapıcı (constructor) yöntemi, kayıtlı tarayıcıları depolamak için bir kayıt defteri (`self._crawlers`) başlatır.
7. `@classmethod def build(cls) -> "CrawlerDispatcher":`: Oluşturucu (builder) yaratıcı desenini kullanarak dispatcher'ı örneklemek ve yapılandırmak için bir sınıf yöntemi tanımlar.
8. `def register_medium(self) -> "CrawlerDispatcher":`: Medium platformu için tarayıcıyı kaydetme yöntemi.
9. `def register(self, domain: str, crawler: type[BaseCrawler]) -> None:`: Genel register() yöntemi, her bir alan adını (domain) normalize eder ve dispatcher'ın self._crawlers kayıt defterine anahtar olarak ekler.
10. `def get_crawler(self, url: str) -> BaseCrawler:`: Verilen bir URL için uygun tarayıcıyı, kayıtlı alan adlarıyla eşleştirerek belirler.

**İngilizce Teknik Terimler (Parantez içinde)**

* URL handling (URL işleme)
* Regex (Düzenli ifadeler)
* Creational pattern (Yaratıcı desen)
* Builder pattern (Oluşturucu deseni)
* Registry (Kayıt defteri)
* Domain (Alan adı)
* Crawler (Tarayıcı)
* Logger (Loglama nesnesi)

---

