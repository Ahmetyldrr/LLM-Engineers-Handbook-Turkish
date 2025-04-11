#The CustomArticleCrawler Sınıfı (The CustomArticleCrawler Class)
The CustomArticleCrawler sınıfı, internetten veri toplamak için farklı bir yaklaşım izler. AsyncHtmlLoader sınıfını kullanarak bir bağlantıdan (link) tüm HTML'i okur ve Html2TextTransformer sınıfını kullanarak bu HTML'den metni çıkarır. Her iki sınıf da langchain_community Python paketi tarafından sağlanır, aşağıdaki kodda görüldüğü gibi, gerekli tüm Python modüllerini içe aktarıyoruz (import): 
```python
from urllib.parse import urlparse 
from langchain_community.document_loaders import AsyncHtmlLoader 
from langchain_community.document_transformers.html2text import Html2TextTransformer 
from loguru import logger 
from llm_engineering.domain.documents import ArticleDocument 
from .base import BaseCrawler
```
**Kod Açıklaması:**
1. `from urllib.parse import urlparse`: urlparse fonksiyonunu içe aktarır, URL'yi ayrıştırmak için kullanılır (URL parsing).
2. `from langchain_community.document_loaders import AsyncHtmlLoader`: AsyncHtmlLoader sınıfını içe aktarır, HTML yüklemek için kullanılır (HTML loading).
3. `from langchain_community.document_transformers.html2text import Html2TextTransformer`: Html2TextTransformer sınıfını içe aktarır, HTML'den metne dönüştürmek için kullanılır (HTML to text transformation).
4. `from loguru import logger`: logger nesnesini içe aktarır, loglama işlemleri için kullanılır (logging).
5. `from llm_engineering.domain.documents import ArticleDocument`: ArticleDocument sınıfını içe aktarır, makale dokümanı için kullanılır (article document).
6. `from .base import BaseCrawler`: BaseCrawler sınıfını içe aktarır, temel crawling işlemleri için kullanılır (base crawler).

Ardından, BaseCrawler'dan miras alan (inherit) CustomArticleCrawler sınıfını tanımlarız. Daha önce olduğu gibi, Selenium tarafından sağlanan kaydırma (scrolling) işlevselliğini veya giriş yapmayı (login) kullanmamıza gerek yoktur. extract metodunda, içeriği çoğaltmaktan kaçınmak için makalenin veritabanında zaten var olup olmadığını kontrol ederiz:
```python
class CustomArticleCrawler(BaseCrawler):
    model = ArticleDocument

    def extract(self, link: str, **kwargs) -> None:
        old_model = self.model.find(link=link)
        if old_model is not None:
            logger.info(f"Article already exists in the database: {link}")
            return
```
**Kod Açıklaması:**
1. `class CustomArticleCrawler(BaseCrawler)`: CustomArticleCrawler sınıfını tanımlar, BaseCrawler'dan miras alır.
2. `model = ArticleDocument`: ArticleDocument sınıfını model olarak atar.
3. `def extract(self, link: str, **kwargs) -> None`: extract metodunu tanımlar, link ve diğer parametreleri alır.
4. `old_model = self.model.find(link=link)`: Veritabanında link ile eşleşen makaleyi arar.
5. `if old_model is not None`: Makale zaten var ise, loglama yapar ve işlemi sonlandırır.

Makale yoksa, scraping işlemine devam ederiz. AsyncHtmlLoader sınıfını kullanarak sağlanan bağlantıdan HTML yükleriz. Ardından, Html2TextTransformer sınıfını kullanarak bunu düz metne (plain text) dönüştürürüz, bu da bir belge listesi (list of documents) döndürür. Sadece ilk belgeye ilgi duyarız. İçeriğin nasıl çıkarıldığı ve ayrıştırıldığı (parsed) konusunda kontrolümüz olmadığı için, bu sınıfı özel olarak uygulanmamış alanlar (domains) için bir yedek sistem olarak kullanırız. Bu iki sınıf, LangChain paradigmasını takip eder, çoğu senaryoda iyi çalışan yüksek seviyeli işlevsellik sağlar (high-level functionality). Hızlı bir şekilde uygulanabilir ancak özelleştirmek zordur (hard to customize). Bu nedenle, birçok geliştirici (developer) üretim kullanım durumlarında (production use cases) LangChain'i kullanmaktan kaçınır:
```python
logger.info(f"Starting scrapping article: {link}")
loader = AsyncHtmlLoader([link])
docs = loader.load()
html2text = Html2TextTransformer()
docs_transformed = html2text.transform_documents(docs)
doc_transformed = docs_transformed[0]
```
**Kod Açıklaması:**
1. `logger.info(f"Starting scrapping article: {link}")`: Makale scraping işleminin başladığını loglar.
2. `loader = AsyncHtmlLoader([link])`: AsyncHtmlLoader nesnesi oluşturur, bağlantıdan HTML yükler.
3. `docs = loader.load()`: HTML yükler ve belgeleri döndürür.
4. `html2text = Html2TextTransformer()`: Html2TextTransformer nesnesi oluşturur, HTML'den metne dönüştürür.
5. `docs_transformed = html2text.transform_documents(docs)`: Belgeleri düz metne dönüştürür.
6. `doc_transformed = docs_transformed[0]`: İlk belgeyi seçer.

Çıkarılan belgeden sayfa içeriğini (page content) ve ilgili meta verileri (metadata) alırız, örneğin başlık (title), alt başlık (subtitle), içerik (content) ve dil (language):
```python
content = {
    "Title": doc_transformed.metadata.get("title"),
    "Subtitle": doc_transformed.metadata.get("description"),
    "Content": doc_transformed.page_content,
    "language": doc_transformed.metadata.get("language"),
}
```
**Kod Açıklaması:**
1. `content = { ... }`: İçerik sözlüğü (dictionary) oluşturur.
2. `"Title": doc_transformed.metadata.get("title")`: Başlığı meta verilerden alır.
3. `"Subtitle": doc_transformed.metadata.get("description")`: Alt başlığı meta verilerden alır.
4. `"Content": doc_transformed.page_content`: İçeriği sayfa içeriğinden alır.
5. `"language": doc_transformed.metadata.get("language")`: Dili meta verilerden alır.

Ardından, URL'yi ayrıştırarak (parsing) makalenin hangi platformdan (veya alan adından) alındığını belirleriz:
```python
parsed_url = urlparse(link)
platform = parsed_url.netloc
```
**Kod Açıklaması:**
1. `parsed_url = urlparse(link)`: URL'yi ayrıştırır.
2. `platform = parsed_url.netloc`: Platformu (veya alan adını) ayrıştırılmış URL'den alır.

Daha sonra, makale modelinin yeni bir örneğini (instance) oluştururuz, çıkarılan içerikle doldururuz ve bu örneği MongoDB veri ambarına (data warehouse) kaydederiz:
```python
user = kwargs["user"]
instance = self.model(
    content=content,
    link=link,
    platform=platform,
    author_id=user.id,
    author_full_name=user.full_name,
)
instance.save()
logger.info(f"Finished scrapping custom article: {link}")
```
**Kod Açıklaması:**
1. `user = kwargs["user"]`: Kullanıcı nesnesini kwargs'dan alır.
2. `instance = self.model(...)`: Makale modelinin yeni bir örneğini oluşturur.
3. `instance.save()`: Örneği veritabanına kaydeder.
4. `logger.info(f"Finished scrapping custom article: {link}")`: Makale scraping işleminin bittiğini loglar.

Bu şekilde, LangChain yardımcı işlevlerini (utility functions) kullanarak GitHub depolarını (repositories) ve rastgele siteleri (random sites) nasıl tarayacağımızı (crawl) gördük. Son olarak, Selenium kullanarak tarayıcıyı (browser) programatik olarak manipüle eden (manipulate) bir tarayıcı (crawler) uygulamasını keşfetmeliyiz. Böylece, MediumCrawler uygulamasıyla devam edeceğiz.

---

