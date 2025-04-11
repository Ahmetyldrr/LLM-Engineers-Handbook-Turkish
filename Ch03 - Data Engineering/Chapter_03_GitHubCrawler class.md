# GithubCrawler Sınıfı
GithubCrawler sınıfı, BaseCrawler'ın işlevselliğini genişleterek GitHub depolarını kazımak (scrape) için tasarlanmıştır. Tarayıcıyı GitHub'a giriş yapmaya gerek kalmadan, Git'in klonlama (clone) işlevselliğinden yararlanabiliriz. Böylece, herhangi bir Selenium işlevselliğinden yararlanmamıza gerek kalmaz. Başlatma (initialization) sırasında, `.git`, `.toml`, `.lock` ve `.png` gibi GitHub depolarında bulunan standart dosya ve dizinleri yok saymak için bir dizi desen (pattern) ayarlar, böylece kazıma işleminden gereksiz dosyalar hariç tutulur:

```python
class GithubCrawler(BaseCrawler):
    model = RepositoryDocument

    def __init__(self, ignore=(".git", ".toml", ".lock", ".png")) -> None:
        super().__init__()
        self._ignore = ignore
```

**Kod Açıklaması:**

*   `class GithubCrawler(BaseCrawler):` GithubCrawler sınıfını tanımlar ve BaseCrawler'dan miras alır.
*   `model = RepositoryDocument` sınıf değişkeni, bu tarayıcı tarafından kullanılan veri modelini tanımlar.
*   `def __init__(self, ignore=(".git", ".toml", ".lock", ".png")) -> None:` sınıfın yapıcı (constructor) metodunu tanımlar. `ignore` parametresi, varsayılan olarak `.git`, `.toml`, `.lock` ve `.png` değerlerini alır.
*   `super().__init__()` üst sınıfın yapıcı metodunu çağırır.
*   `self._ignore = ignore` yok sayılacak desenleri örnek değişkenine atar.

Daha sonra, `extract()` metodunu uygularız, burada tarayıcı önce deponun zaten işlenip veritabanına kaydedilip kaydedilmediğini kontrol eder. Eğer mevcutsa, yinelenenlerin saklanmasını önlemek için metottan çıkar:

```python
def extract(self, link: str, **kwargs) -> None:
    old_model = self.model.find(link=link)
    if old_model is not None:
        logger.info(f"Repository already exists in the database: {link}")
        return
```

**Kod Açıklaması:**

*   `def extract(self, link: str, **kwargs) -> None:` `extract` metodunu tanımlar, bu metod bir depo linki alır ve işlemleri gerçekleştirir.
*   `old_model = self.model.find(link=link)` verilen link ile eşleşen bir depo modelini veritabanında arar.
*   `if old_model is not None:` eğer böyle bir depo zaten varsa, bir mesaj loglar ve metottan çıkar.

Depo yeni ise, tarayıcı depo adını linkten çıkarır. Ardından, deponun klonlanacağı geçici bir dizin oluşturur, böylece klonlanan depo işlendikten sonra yerel diskten temizlenir:

```python
logger.info(f"Starting scrapping GitHub repository: {link}")
repo_name = link.rstrip("/").split("/")[-1]
local_temp = tempfile.mkdtemp()
```

**Kod Açıklaması:**

*   `logger.info(f"Starting scrapping GitHub repository: {link}")` kazıma işleminin başladığını loglar.
*   `repo_name = link.rstrip("/").split("/")[-1]` depo linkinden depo adını çıkarır.
*   `local_temp = tempfile.mkdtemp()` geçici bir dizin oluşturur.

Bir `try` bloğu içinde, tarayıcı geçerli çalışma dizinini geçici dizine değiştirir ve `git clone` komutunu farklı bir işlemde yürütür:

```python
try:
    os.chdir(local_temp)
    subprocess.run(["git", "clone", link])
```

**Kod Açıklaması:**

*   `try:` bir deneme bloğu başlatır.
*   `os.chdir(local_temp)` çalışma dizinini geçici dizine değiştirir.
*   `subprocess.run(["git", "clone", link])` `git clone` komutunu çalıştırarak deponun klonlanmasını sağlar.

Depo başarıyla klonlandıktan sonra, tarayıcı klonlanan deponun yolunu oluşturur. Standart bir şekilde dosya içeriklerini toplamak için boş bir sözlük (dictionary) başlatır. Dizin ağacını dolaşırken, yok sayma desenleriyle eşleşen herhangi bir dizin veya dosyayı atlar. Her ilgili dosya için içeriği okur, boşlukları kaldırır ve sözlükte dosya yolunu anahtar olarak kullanarak saklar:

```python
repo_path = os.path.join(local_temp, os.listdir(local_temp)[0])
tree = {}
for root, _, files in os.walk(repo_path):
    dir = root.replace(repo_path, "").lstrip("/")
    if dir.startswith(self._ignore):
        continue
    for file in files:
        if file.endswith(self._ignore):
            continue
        file_path = os.path.join(dir, file)
        with open(os.path.join(root, file), "r", errors="ignore") as f:
            tree[file_path] = f.read().replace(" ", "")
```

**Kod Açıklaması:**

*   `repo_path = os.path.join(local_temp, os.listdir(local_temp)[0])` klonlanan deponun tam yolunu oluşturur.
*   `tree = {}` dosya içeriklerini saklamak için boş bir sözlük başlatır.
*   `for root, _, files in os.walk(repo_path):` deponun dizin ağacını dolaşmaya başlar.
*   İç içe döngüler ve koşullar, ilgili dosyaları bulur, içeriklerini okur ve sözlüğe ekler.

Daha sonra, `RepositoryDocument` modelinin yeni bir örneğini oluşturur, depo içeriği, adı, linki, platform bilgileri ve yazar ayrıntılarıyla doldurur. Örnek daha sonra MongoDB'ye kaydedilir:

```python
user = kwargs["user"]
instance = self.model(
    content=tree,
    name=repo_name,
    link=link,
    platform="github",
    author_id=user.id,
    author_full_name=user.full_name,
)
instance.save()
```

**Kod Açıklaması:**

*   `user = kwargs["user"]` `kwargs` içinden kullanıcı nesnesini alır.
*   `instance = self.model(...)` `RepositoryDocument` modelinin yeni bir örneğini oluşturur ve gerekli alanları doldurur.
*   `instance.save()` örneği MongoDB veritabanına kaydeder.

Son olarak, kazıma işlemi başarılı olsun veya bir istisna oluşsun, tarayıcı geçici dizini kaldırarak işlem sırasında kullanılan kaynakları temizler:

```python
except Exception:
    raise
finally:
    shutil.rmtree(local_temp)
logger.info(f"Finished scrapping GitHub repository: {link}")
```

**Kod Açıklaması:**

*   `except Exception:` herhangi bir istisna yakalar.
*   `raise` yakalanan istisnayı yeniden fırlatır.
*   `finally:` bloğu her durumda çalıştırılır.
*   `shutil.rmtree(local_temp)` geçici dizini ve içeriğini siler.
*   `logger.info(f"Finished scrapping GitHub repository: {link}")` kazıma işleminin tamamlandığını loglar.

**İngilizce Teknik Terimlerin Türkçeleri ve Parantez İçinde İngilizce Karşılıkları:**

*   Kazımak (Scrape)
*   Depo (Repository)
*   Klonlama (Clone)
*   Desen (Pattern)
*   Başlatma (Initialization)
*   Veri Modeli (Data Model)
*   Yapıcı Metod (Constructor Method)
*   Yok Sayma (Ignore)
*   Loglama (Logging)
*   Geçici Dizin (Temporary Directory)
*   Dizin Ağacı (Directory Tree)
*   Sözlük (Dictionary)
*   İstisna (Exception)
*   Yeniden Fırlatma (Re-raising)

# GithubCrawler Sınıfının Tam Türkçe Kod Çevirisi

```python
# GithubCrawler Sınıfı
class GithubCrawler(BaseCrawler):
    model = RepositoryDocument  # Veri modeli

    def __init__(self, ignore=(".git", ".toml", ".lock", ".png")) -> None:
        # Yapıcı metod, varsayılan yok sayma desenleriyle
        super().__init__()
        self._ignore = ignore  # Yok sayma desenleri

    def extract(self, link: str, **kwargs) -> None:
        # Kazıma işlemi
        old_model = self.model.find(link=link)  # Mevcut depo modeli
        if old_model is not None:
            # Depo zaten varsa logla ve çık
            logger.info(f"Depo zaten veritabanında mevcut: {link}")
            return

        logger.info(f"GitHub deposu kazımaya başlandı: {link}")
        repo_name = link.rstrip("/").split("/")[-1]  # Depo adı
        local_temp = tempfile.mkdtemp()  # Geçici dizin

        try:
            os.chdir(local_temp)  # Çalışma dizinini değiştir
            subprocess.run(["git", "clone", link])  # Depoyu klonla

            repo_path = os.path.join(local_temp, os.listdir(local_temp)[0])
            tree = {}  # Dosya içerikleri için sözlük
            for root, _, files in os.walk(repo_path):
                dir = root.replace(repo_path, "").lstrip("/")
                if dir.startswith(self._ignore):
                    continue  # Yok sayma desenlerine uyuyorsa atla
                for file in files:
                    if file.endswith(self._ignore):
                        continue  # Yok sayma desenlerine uyuyorsa atla
                    file_path = os.path.join(dir, file)
                    with open(os.path.join(root, file), "r", errors="ignore") as f:
                        # Dosya içeriğini oku ve sözlüğe ekle
                        tree[file_path] = f.read().replace(" ", "")

            user = kwargs["user"]  # Kullanıcı nesnesi
            instance = self.model(
                content=tree,  # Depo içeriği
                name=repo_name,  # Depo adı
                link=link,  # Depo linki
                platform="github",  # Platform bilgisi
                author_id=user.id,  # Yazar ID'si
                author_full_name=user.full_name,  # Yazar tam adı
            )
            instance.save()  # Veritabanına kaydet

        except Exception:
            raise  # İstisnayı yeniden fırlat
        finally:
            shutil.rmtree(local_temp)  # Geçici dizini sil
        logger.info(f"GitHub deposu kazıma işlemi tamamlandı: {link}")
```

---

