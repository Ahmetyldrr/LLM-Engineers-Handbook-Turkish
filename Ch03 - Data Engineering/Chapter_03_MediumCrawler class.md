# Kodun Başlangıcı
Kod, temel kütüphaneleri içe aktararak (import ederek) ve `BaseSeleniumCrawler`'dan miras alan `MediumCrawler` sınıfını tanımlayarak başlar: 
```python
from bs4 import BeautifulSoup 
from loguru import logger 
from llm_engineering.domain.documents import ArticleDocument 
from .base import BaseSeleniumCrawler 

class MediumCrawler(BaseSeleniumCrawler):
    model = ArticleDocument
```
- `from bs4 import BeautifulSoup`: HTML veya XML dokümanlarını ayrıştırmak (parse etmek) için kullanılan `BeautifulSoup` sınıfını içe aktarır.
- `from loguru import logger`: Loglama işlemleri için kullanılan `logger` nesnesini içe aktarır.
- `from llm_engineering.domain.documents import ArticleDocument`: `ArticleDocument` sınıfını içe aktarır, bu sınıf muhtemelen makale verilerini temsil eden bir belge sınıfıdır.
- `from .base import BaseSeleniumCrawler`: `BaseSeleniumCrawler` sınıfını içe aktarır, bu sınıf muhtemelen Selenium ile tarama işlemleri için temel bir sınıftır.

# `set_extra_driver_options` Metodu
`MediumCrawler` sınıfı içinde, Selenium tarafından kullanılan varsayılan sürücü seçeneklerini genişletmek için `set_extra_driver_options()` metodunu kullanırız:
```python
def set_extra_driver_options(self, options) -> None:
    options.add_argument(r"--profile-directory=Profile 2")
```
- `def set_extra_driver_options(self, options) -> None:`: Sürücü seçeneklerini ayarlamak için kullanılan metodun tanımı.
- `options.add_argument(r"--profile-directory=Profile 2")`: Sürücüye ekstra bir argüman ekler, bu örnekte profil dizinini "Profile 2" olarak ayarlar.

# `extract` Metodu
`extract()` metodu, temel işlevselliği uygular, önce makalenin veritabanında zaten var olup olmadığını kontrol eder (yinelenen girişleri önlemek için). Eğer makale yeni ise, metod makalenin bağlantısına giderek sayfayı kaydırır ve tüm içeriğin yüklenmesini sağlar:
```python
def extract(self, link: str, **kwargs) -> None:
    old_model = self.model.find(link=link)
    if old_model is not None:
        logger.info(f"Article already exists in the database: {link}")
        return
    logger.info(f"Starting scrapping Medium article: {link}")
    self.driver.get(link)
    self.scroll_page()
```
- `old_model = self.model.find(link=link)`: Veritabanında aynı bağlantıya sahip bir makale arar.
- `if old_model is not None:`: Eğer makale zaten varsa, loglar ve işlemi sonlandırır.
- `self.driver.get(link)`: Sürücüye belirtilen bağlantıya gitmesini söyler.
- `self.scroll_page()`: Sayfayı kaydırarak içeriğin yüklenmesini sağlar.

# İçerik Çıkarma
Sayfa tamamen yüklendikten sonra, metod `BeautifulSoup` kullanarak HTML içeriği ayrıştırır ve makalenin başlığını, alt başlığını ve tam metnini çıkarır. `BeautifulSoup`, web kazıma (web scraping) ve HTML veya XML dokümanlarını ayrıştırmak için popüler bir Python kütüphanesidir. Böylece, Selenium ile erişilen HTML'den ihtiyacımız olan tüm HTML öğelerini çıkarmak için kullandık. Son olarak, her şeyi bir sözlükte toplarız:
```python
soup = BeautifulSoup(self.driver.page_source, "html.parser")
title = soup.find_all("h1", class_="pw-post-title")
subtitle = soup.find_all("h2", class_="pw-subtitle-paragraph")
data = {
    "Title": title[0].string if title else None,
    "Subtitle": subtitle[0].string if subtitle else None,
    "Content": soup.get_text(),
}
```
- `soup = BeautifulSoup(self.driver.page_source, "html.parser")`: Sayfa kaynağını `BeautifulSoup` ile ayrıştırır.
- `title = soup.find_all("h1", class_="pw-post-title")`: Başlık öğelerini bulur.
- `subtitle = soup.find_all("h2", class_="pw-subtitle-paragraph")`: Alt başlık öğelerini bulur.
- `data = {...}`: Çıkarılan verileri bir sözlükte toplar.

# Kaynakları Serbest Bırakma ve Veritabanına Kaydetme
Metod, kaynakları serbest bırakmak için WebDriver'ı kapatır. Ardından, yeni bir `ArticleDocument` örneği oluşturur, bunu çıkarılan içerik ve kullanıcı bilgileriyle doldurur ve veritabanına kaydeder:
```python
self.driver.close()
user = kwargs["user"]
instance = self.model(
    platform="medium",
    content=data,
    link=link,
    author_id=user.id,
    author_full_name=user.full_name,
)
instance.save()
logger.info(f"Successfully scraped and saved article: {link}")
```
- `self.driver.close()`: WebDriver'ı kapatır.
- `instance = self.model(...)`: Yeni bir belge örneği oluşturur.
- `instance.save()`: Belgeyi veritabanına kaydeder.

# Diğer Kazıyıcılar (Crawlers)
LinkedIn kazıyıcısı, Medium kazıyıcısına benzer bir deseni takip eder, burada Selenium'u giriş yapmak ve bir kullanıcının en son gönderilerinin akışına erişmek için kullanır. Ardından, gönderileri çıkarır ve bir sınır ulaşana kadar akışı kaydırarak sonraki sayfayı yükler. Tam uygulamayı https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/application/crawlers/linkedin.py adresinde bulabilirsiniz.

# Yüksek Seviyeli Araçlar
LLM'lerin (Large Language Models) yükselişiyle birlikte, internetten veri toplamak birçok gerçek dünya AI uygulamasında kritik bir adım haline geldi. Bu nedenle, Python ekosisteminde Scrapy (https://github.com/scrapy/scrapy) gibi daha yüksek seviyeli araçlar ortaya çıktı. Scrapy, web sitelerini kazır ve sayfalarından yapılandırılmış verileri çıkarır. Ayrıca, LLM'ler ve AI uygulamaları için veri kazımada oldukça özelleşmiş olan Crawl4AI (https://github.com/unclecode/crawl4ai) gibi araçlar da mevcuttur.

# Sonuç
Bu bölümde, üç tür kazıyıcının uygulanmasını inceledik: biri GitHub depolarını klonlamak için `git` yürütülebilir dosyasını alt süreçte kullanan, biri tek bir web sayfasının HTML'sini çıkarmak için LangChain yardımcı programlarını kullanan ve biri daha karmaşık senaryolar için Selenium'u kullanan. Son adım, bu bölümde kullandığımız belge sınıflarının nasıl çalıştığını anlamaktır, örneğin `ArticleDocument`.

---

