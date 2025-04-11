#LLM Twin'in Veri Toplama ETL Mimarisini Anlamak
Uygulamaya dalmadan önce, Şekil 3.1'de gösterilen LLM Twin'in veri toplama ETL (Extract, Transform, Load) mimarisini anlamalıyız. Hangi platformlardan veri çekeceğimizi ve veri yapılarını ve süreçlerini nasıl tasarlayacağımızı keşfetmeliyiz. Ancak ilk adım, veri toplama işlem hattının (pipeline) bir ETL sürecine nasıl eşlendiğini anlamaktır. Bir ETL işlem hattı üç temel adımdan oluşur: 
- Çeşitli kaynaklardan veri çıkarırız (Extract).
- Verileri temizleyerek ve standardize ederek tutarlı bir formata dönüştürürüz (Transform).
- Dönüştürülen verileri bir veri ambarına (Data Warehouse) veya veritabanına yükleriz (Load).

Projemiz için NoSQL veri ambarı olarak MongoDB kullanıyoruz. Bu standart bir yaklaşım olmamasına rağmen, bu seçimin arkasındaki mantığı kısa sürede açıklayacağız.

#Şekil 3.1: LLM Twin'in Veri Toplama ETL İşlem Hattı Mimari

Giriş olarak bir kullanıcı ve bir liste bağlantı alan bir ETL işlem hattı tasarlamak istiyoruz. Ardından, her bağlantıyı tek tek tarayarak (crawl), toplanan içeriği standardize eder ve bunu MongoDB veri ambarında belirli bir yazarın altında kaydeder. Bu nedenle, veri toplama işlem hattının imzası (signature) aşağıdaki gibi görünecektir:

- Giriş: Bir liste bağlantı ve ilişkili kullanıcı (yazar)
- Çıkış: NoSQL veri ambarında depolanan ham belgeler listesi

Kullanıcı ve yazar terimlerini birbirinin yerine kullanacağız, çünkü ETL işlem hattında çoğu senaryoda bir kullanıcı, çıkarılan içeriğin yazarıdır. Ancak veri ambarında, yalnızca bir kullanıcı koleksiyonu (collection) vardır.

ETL işlem hattı, her bağlantının alan adını (domain) tespit edecek ve buna dayanarak özel bir tarayıcı (crawler) çağıracaktır. Şekil 3.2'de görüldüğü gibi, üç farklı veri kategorisi için dört farklı tarayıcı uyguladık.

İlk olarak, kitap boyunca çalışacağımız üç temel veri kategorisini keşfedeceğiz. Toplanan tüm belgelerimiz bir makale, depo (veya kod) ve gönderiye indirgenebilir. Verilerin nereden geldiği önemli değildir; esas olarak belgenin formatıyla ilgileniyoruz. Çoğu senaryoda, bu veri kategorilerini farklı şekilde işlememiz gerekecektir. Bu nedenle, her biri için farklı bir alan varlığı (domain entity) oluşturduk; her varlık MongoDB'de kendi sınıfına ve koleksiyonuna sahip olacaktır.

#Şekil 3.2: Tarayıcılar ve Veri Kategorileri Arasındaki İlişki

Kod tabanımız dört farklı tarayıcıyı destekler:
1. **Medium Tarayıcı**: Medium'dan veri toplamak için kullanılır. Bir makale belgesi çıktılar. Medium'a giriş yapar ve makalenin HTML'sini tarar. Ardından, HTML'den metni çıkarır, temizler ve normalleştirir ve makalenin standardize edilmiş metnini NoSQL veri ambarına yükler.
2. **Özel Makale Tarayıcı**: Medium tarayıcısına benzer adımları gerçekleştirir, ancak çeşitli sitelerden makale toplamak için daha genel bir uygulamadır. Bu nedenle, herhangi bir platformun özelliklerini uygulamadığından, giriş yapma adımını gerçekleştirmez ve belirli bir bağlantıdan tüm HTML'yi körü körüne toplar. Bu, çevrimiçi olarak serbestçe bulunabilen makaleler için yeterlidir.
3. **GitHub Tarayıcı**: GitHub'dan veri toplar. Bir depo belgesi çıktılar. Depoyu klonlar, depo dosya ağacını ayrıştırır (parse), dosyaları temizler ve normalleştirir ve bunları veritabanına yükler.
4. **LinkedIn Tarayıcı**: LinkedIn'den veri toplamak için kullanılır. Birden fazla gönderi belgesi çıktılar. LinkedIn'a giriş yapar, kullanıcının akışına gider ve kullanıcının en son gönderilerini tarar. Her gönderi için HTML'sini çıkarır, temizler ve normalleştirir ve MongoDB'ye yükler.

Her tarayıcı, belirli bir platform veya siteye belirli bir şekilde erişir ve ondan HTML çıkarır. Ardından, tüm tarayıcılar HTML'yi ayrıştırır, ondan metni çıkarır ve temizler ve normalleştirir, böylece aynı arayüz altında veri ambarında depolanabilir.

Tüm toplanan verileri üç veri kategorisine indirgeyerek ve her yeni veri kaynağı için yeni bir veri kategorisi oluşturmayarak, bu mimariyi minimum çabayla birden fazla veri kaynağına genişletebiliriz. Örneğin, X'ten veri toplamaya başlamak istersek, yalnızca bir gönderi belgesi çıkaran yeni bir tarayıcı uygulamamız yeterlidir.

#Kod Açıklaması
Aşağıdaki kod, yukarıda açıklanan ETL işlem hattının temel yapısını temsil etmektedir:
```python
# Giriş verilerini temsil eden sınıf
class InputData:
    def __init__(self, user, links):
        self.user = user
        self.links = links

# ETL işlem hattını temsil eden sınıf
class ETLPipeline:
    def __init__(self, input_data):
        self.input_data = input_data

    def extract(self):
        # Her bağlantı için uygun tarayıcıyı çağır
        for link in self.input_data.links:
            crawler = self.get_crawler(link)
            raw_data = crawler.crawl(link)
            # Ham veriyi işle ve MongoDB'ye kaydet
            processed_data = self.transform(raw_data)
            self.load(processed_data)

    def get_crawler(self, link):
        # Bağlantının alan adına göre uygun tarayıcıyı döndür
        if link.startswith("https://medium.com"):
            return MediumCrawler()
        elif link.startswith("https://github.com"):
            return GitHubCrawler()
        # Diğer tarayıcılar için benzer koşullar
        else:
            return CustomArticleCrawler()

    def transform(self, raw_data):
        # Ham veriyi temizle ve standardize et
        cleaned_data = clean_data(raw_data)
        standardized_data = standardize_data(cleaned_data)
        return standardized_data

    def load(self, processed_data):
        # İşlenmiş veriyi MongoDB'ye yükle
        mongo_db = MongoDB()
        mongo_db.save(processed_data, self.input_data.user)

# Tarayıcı sınıflarını temsil eden temel sınıf
class Crawler:
    def crawl(self, link):
        # Bağlantıdan HTML içeriğini çıkar
        html_content = self.get_html_content(link)
        # HTML içeriğinden metni çıkar, temizle ve normalleştir
        text = self.extract_text(html_content)
        cleaned_text = clean_text(text)
        normalized_text = normalize_text(cleaned_text)
        return normalized_text

    def get_html_content(self, link):
        # Bağlantıdan HTML içeriğini al
        # Bu method alt sınıflar tarafından uygulanmalıdır
        pass

    def extract_text(self, html_content):
        # HTML içeriğinden metni çıkar
        # Bu method alt sınıflar tarafından uygulanmalıdır
        pass

# Medium, GitHub, LinkedIn ve Özel Makale tarayıcıları için Crawler sınıfını genişleten sınıflar
class MediumCrawler(Crawler):
    def get_html_content(self, link):
        # Medium'a giriş yap ve HTML içeriğini al
        # ...

class GitHubCrawler(Crawler):
    def get_html_content(self, link):
        # GitHub deposunu klonla ve HTML içeriğini al
        # ...

class LinkedInCrawler(Crawler):
    def get_html_content(self, link):
        # LinkedIn'a giriş yap ve HTML içeriğini al
        # ...

class CustomArticleCrawler(Crawler):
    def get_html_content(self, link):
        # Bağlantıdan HTML içeriğini al (giriş yapmadan)
        # ...
```
Yukarıdaki kod, ETL işlem hattının temel yapısını ve farklı tarayıcı uygulamalarını temsil etmektedir. Her tarayıcı, belirli bir platformdan veri toplamak için özelleştirilmiştir ve ham veriyi temizleyip standardize ederek MongoDB'ye yükler.

#Neden MongoDB Kullanıldı?
MongoDB'yi veri ambarı olarak kullanmak yaygın bir yaklaşım değildir, ancak projemizde küçük miktarda veri ile çalıştığımız için MongoDB bu işi halledebilir. Ayrıca, yapılandırılmamış metin verileriyle çalışırken şema zorlamayan (schema-less) bir NoSQL veritabanı kullanmak geliştirme sürecini kolaylaştırdı ve hızlandırdı. MongoDB'nin stabilitesi, kullanım kolaylığı ve sezgisel Python SDK'sı da bu tercihte etkili oldu. Ancak büyük veri ile çalışırken, Snowflake veya BigQuery gibi özel bir veri ambarı kullanmak ideal olacaktır.

Artık LLM Twin'in veri toplama işlem hattının mimarisini anladığımıza göre, uygulamaya geçebiliriz.

---

