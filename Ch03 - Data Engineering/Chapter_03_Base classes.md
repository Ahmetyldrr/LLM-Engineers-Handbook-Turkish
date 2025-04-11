# BaseCrawler Arayüzü (Interface)

Şimdi, https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/application/crawlers/base.py adresinde bulunan BaseCrawler arayüzünü inceleyelim.

```python
from abc import ABC, abstractmethod

class BaseCrawler(ABC):
    model: type[NoSQLBaseDocument]  # Veri kategorisi belge türü (data category document type)

    @abstractmethod
    def extract(self, link: str, **kwargs) -> None:
        ...
```

Yukarıda belirtildiği gibi, arayüz bir `extract()` yöntemi tanımlar ve bu yöntem bir bağlantı (link) alır. Ayrıca, sınıf düzeyinde (`class level`) MongoDB veri ambarına (`data warehouse`) çıkarılan verileri kaydetmek için kullanılan veri kategorisi belge türünü (`data category document type`) temsil eden bir `model` özniteliği (`attribute`) tanımlar. Bunu yaparak, her alt sınıfı (`subclass`) farklı veri kategorileriyle (`data categories`) özelleştirmemize (`customize`) olanak tanır ve aynı öznitelikleri (`attributes`) sınıf düzeyinde (`class level`) korur. `NoSQLBaseDocument` sınıfını (`class`) belge varlıklarına (`document entities`) dalarken yakında inceleyeceğiz.

Ayrıca, `BaseCrawler` sınıfını (`class`), çeşitli siteleri (`sites`) örneğin Medium veya LinkedIn'i taramak (`crawl`) için Selenium'u (`Selenium`) kullanan yeniden kullanılabilir (`reusable`) işlevselliği (`functionality`) uygulayan (`implementing`) `BaseSeleniumCrawler` sınıfıyla (`class`) genişletiyoruz (`extend`). Selenium, web tarayıcılarını (`web browsers`) otomatize (`automate`) etmek için bir araçtır (`tool`). Web sayfalarıyla (`web pages`) programlı (`programmatically`) olarak etkileşimde (`interact`) bulunmak için kullanılır (örneğin LinkedIn'e giriş yapmak, profiller arasında gezinmek vb.). Selenium, Chrome, Firefox veya Brave gibi çeşitli tarayıcıları (`browsers`) programlı olarak kontrol edebilir (`control`).

Bu özel platformlar (`platforms`) için, tarayıcıyı (`browser`) programlı olarak (`programmatically`) giriş yapmak (`log in`) ve haber akışını (`newsfeed`) veya makaleyi (`article`) tüm HTML'yi (`HTML`) çıkarmadan (`extract`) önce kaydırma (`scroll`) işlemi için Selenium'a ihtiyacımız var. Diğer siteler (`sites`) için, giriş (`login`) adımından geçmek (`go through`) zorunda olmadığımız veya tüm sayfayı (`whole page`) doğrudan (`directly`) yükleyebileceğimiz (`load`) yerlerde, daha basit (`straightforward`) yöntemlerle (`methods`) belirli bir URL'den (`URL`) HTML'yi (`HTML`) çıkarabiliriz (`extract`).

Selenium tabanlı (`Selenium-based`) tarayıcıların (`crawlers`) çalışması için, makinenizde (`machine`) Chrome (veya Brave gibi Chromium tabanlı (`Chromium-based`) bir tarayıcı) kurulu (`installed`) olmalıdır.

Kod, Selenium ve ChromeDriver başlatıcısı (`initializer`) kullanarak web tarama (`web crawling`) için gerekli içe aktarmaları (`imports`) ve yapılandırmaları (`configurations`) ayarlayarak başlar. `chromedriver_autoinstaller`, ChromeDriver'ın (`ChromeDriver`) uygun sürümünün (`version`) yüklenmesini (`installed`) ve sistem yoluna (`system path`) eklenmesini (`added`) sağlar, böylece yüklü (`installed`) Google Chrome tarayıcı (`browser`) sürümüyle (`version`) uyumluluğu (`compatibility`) sağlar.

```python
import time
from tempfile import mkdtemp
import chromedriver_autoinstaller
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from llm_engineering.domain.documents import NoSQLBaseDocument

# chromedriver'ın mevcut sürümünün olup olmadığını kontrol edin
# ve eğer yoksa, otomatik olarak indirin,
# sonra chromedriver'ı yola ekleyin
chromedriver_autoinstaller.install()
```

# BaseSeleniumCrawler Sınıfı

Daha sonra, Medium veya LinkedIn gibi sitelerden (`sites`) veri toplamak (`collect data`) gibi kullanım durumları (`use cases`) için Selenium'a (`Selenium`) ihtiyaç duyduğumuz yerlerde kullanmak üzere `BaseSeleniumCrawler` sınıfını (`class`) tanımlarız.

```python
class BaseSeleniumCrawler(BaseCrawler, ABC):
    def __init__(self, scroll_limit: int = 5) -> None:
        options = webdriver.ChromeOptions()
        
        options.add_argument("--no-sandbox")
        options.add_argument("--headless=new")
        options.add_argument("--disable-dev-shm-usage")
        options.add_argument("--log-level=3")
        options.add_argument("--disable-popup-blocking")
        options.add_argument("--disable-notifications")
        options.add_argument("--disable-extensions")
        options.add_argument("--disable-background-networking")
        options.add_argument("--ignore-certificate-errors")
        options.add_argument(f"--user-data-dir={mkdtemp()}")
        options.add_argument(f"--data-path={mkdtemp()}")
        options.add_argument(f"--disk-cache-dir={mkdtemp()}")
        options.add_argument("--remote-debugging-port=9226")
```

Chrome seçeneklerini (`Chrome options`) yapılandırdıktan (`configuring`) sonra, kod (`code`), alt sınıfların (`subclasses`) `set_extra_driver_options()` yöntemini (`method`) çağırarak (`calling`) ek sürücü seçeneklerini (`extra driver options`) ayarlamasına (`set`) olanak tanır. Daha sonra, kaydırma sınırını (`scroll limit`) başlatır (`initialize`) ve belirtilen seçeneklerle (`specified options`) yeni bir Chrome sürücüsü (`Chrome driver`) örneği (`instance`) oluşturur (`create`).

```python
self.set_extra_driver_options(options)
self.scroll_limit = scroll_limit
self.driver = webdriver.Chrome(
    options=options,
)
```

`BaseSeleniumCrawler` sınıfı (`class`), `set_extra_driver_options()` ve `login()` için yer tutucu (`placeholder`) yöntemleri (`methods`) içerir, bu yöntemleri (`methods`) alt sınıflar (`subclasses`) geçersiz kılarak (`override`) belirli işlevselliği (`specific functionality`) sağlayabilir (`provide`). Bu, her platformun (`platform`) farklı bir giriş sayfasına (`login page`) ve farklı bir HTML yapısına (`HTML structure`) sahip olması nedeniyle modülerliği (`modularity`) sağlar.

```python
def set_extra_driver_options(self, options: Options) -> None:
    pass

def login(self) -> None:
    pass
```

Son olarak, `scroll_page()` yöntemi (`method`), LinkedIn gibi siteler (`sites`) için belirtilen kaydırma sınırına (`scroll limit`) kadar sayfaları (`pages`) kaydırma (`scroll`) mekanizması (`mechanism`) uygular (`implements`). Sayfanın (`page`) altına (`bottom`) kaydırır (`scroll`), yeni içeriğin (`new content`) yüklenmesini (`load`) bekler (`wait`) ve işlemi (`process`) sayfa sonuna (`end of page`) ulaşana (`reach`) veya kaydırma sınırını (`scroll limit`) aşana (`exceed`) kadar tekrarlar (`repeat`). Bu yöntem (`method`), kullanıcının (`user`) kaydırma (`scroll`) işlemi yaptıkça içeriğin (`content`) göründüğü (`appear`) beslemeler (`feeds`) için önemlidir.

```python
def scroll_page(self) -> None:
    """LinkedIn sayfasını kaydırma sınırına göre kaydırın."""
    current_scroll = 0
    last_height = self.driver.execute_script("return document.body.scrollHeight")
    while True:
        self.driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
        time.sleep(5)
        new_height = self.driver.execute_script("return document.body.scrollHeight")
        if new_height == last_height or (self.scroll_limit and current_scroll >= self.scroll_limit):
            break
        last_height = new_height
        current_scroll += 1
```

## Kod Açıklaması

1. `from abc import ABC, abstractmethod`: Bu satır, Python'ın soyut temel sınıflar (`abstract base classes`) modülünden (`module`) `ABC` ve `@abstractmethod` dekoratörünü (`decorator`) içe aktarır (`import`).

2. `class BaseCrawler(ABC)`: Bu satır, `BaseCrawler` adlı bir soyut temel sınıf (`abstract base class`) tanımlar.

3. `model: type[NoSQLBaseDocument]`: Bu satır, `model` adlı bir sınıf özniteliği (`class attribute`) tanımlar ve bu öznitelik (`attribute`), MongoDB veri ambarına (`data warehouse`) kaydedilecek verilerin (`data`) kategorisini (`category`) temsil eden (`represent`) belge türünü (`document type`) belirtir (`specify`).

4. `@abstractmethod def extract(self, link: str, **kwargs) -> None`: Bu satır, alt sınıflar (`subclasses`) tarafından uygulanması (`implemented`) gereken (`required`) soyut (`abstract`) bir `extract()` yöntemi (`method`) tanımlar.

5. `class BaseSeleniumCrawler(BaseCrawler, ABC)`: Bu satır, `BaseSeleniumCrawler` adlı bir sınıf (`class`) tanımlar ve bu sınıf (`class`), `BaseCrawler` sınıfından (`class`) miras alır (`inherit`) ve soyut (`abstract`) bir sınıftır (`class`).

6. `options = webdriver.ChromeOptions()`: Bu satır, Chrome tarayıcısı (`Chrome browser`) için seçenekleri (`options`) yapılandırmak (`configure`) üzere bir `ChromeOptions` örneği (`instance`) oluşturur (`create`).

7. `options.add_argument("--headless=new")`: Bu satır, Chrome'u (`Chrome`) headless (`headless`) modda (`mode`) çalıştırmak (`run`) için bir seçenek (`option`) ekler (`add`).

8. `self.driver = webdriver.Chrome(options=options)`: Bu satır, belirtilen seçeneklerle (`specified options`) bir Chrome sürücüsü (`Chrome driver`) örneği (`instance`) oluşturur (`create`).

9. `def scroll_page(self) -> None`: Bu satır, sayfayı (`page`) kaydırma (`scroll`) işlemini (`operation`) uygulayan (`implement`) bir yöntem (`method`) tanımlar.

10. `self.driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")`: Bu satır, sayfanın (`page`) altına (`bottom`) kaydırma (`scroll`) işlemini (`operation`) gerçekleştirmek (`perform`) için JavaScript'i (`JavaScript`) yürütür (`execute`).

---

