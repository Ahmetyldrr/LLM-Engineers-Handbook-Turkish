# Yazılım Desenlerini Anlamak: ORM ve ODM
Yazılım desenlerinden bahsetmeden önce, ORM'in ne olduğuna bakalım. ORM, nesne yönelimli paradigma (Object-Oriented Paradigm) kullanarak bir veritabanından veri sorgulamanıza ve manipüle etmenize olanak tanıyan bir tekniktir. SQL veya API-özel sorgular yazmak yerine, tüm karmaşıklığı, çoğu zaman CRUD işlemleri olmak üzere tüm veritabanı işlemlerini nasıl yapacağını bilen bir ORM sınıfı altında kapsülersiniz. Böylece, ORM ile çalışmak, veritabanı işlemlerini manuel olarak yapma ihtiyacını ortadan kaldırır ve boilerplate kodunu manuel olarak yazma ihtiyacını azaltır. Bir ORM, PostgreSQL veya MySQL gibi bir SQL veritabanıyla etkileşime girer. Çoğu modern Python uygulaması, veritabanıyla etkileşimde bulunurken ORM'leri kullanır. Veri dünyasında SQL hala popüler bir seçim olsa da, Python arka uç bileşenlerinde nadiren ham SQL sorguları görürsünüz. En popüler Python ORM'i SQLAlchemy'dir (https://www.sqlalchemy.org/). Ayrıca, FastAPI'nin yükselişiyle birlikte, SQLAlchemy üzerine bir sarmalayıcı olan ve FastAPI ile entegrasyonu kolaylaştıran SQLModel (https://github.com/fastapi/sqlmodel) de yaygın bir seçimdir.

Örneğin, SQLAlchemy kullanarak, ID ve isim alanlarına sahip bir Kullanıcı (User) ORM'i tanımladık. Kullanıcı ORM'i, SQL veritabanındaki kullanıcılar tablosuna eşlenir. Böylece, yeni bir kullanıcı oluşturup veritabanına kaydetdiğimizde, otomatik olarak kullanıcılar tablosuna kaydedilir. Aynı şey, Kullanıcı sınıfı üzerindeki tüm CRUD işlemleri için de geçerlidir.

```python
from sqlalchemy import Column, Integer, String, create_engine
from sqlalchemy.orm import declarative_base, sessionmaker

# Tanımlayıcı temel sınıfı (declarative base)
Base = declarative_base()

# Kullanıcılar tablosuna eşlenen bir sınıf tanımlar.
class User(Base):
    # Tablo adı (table name)
    __tablename__ = "users"
    # ID alanı (ID field)
    id = Column(Integer, primary_key=True)
    # İsim alanı (name field)
    name = Column(String)

# Kullanıcı ORM'i kullanarak, SQL yazmadan doğrudan Python'dan kullanıcıları ekleyebilir veya sorgulayabiliriz.
# ORM genellikle tüm CRUD işlemlerini destekler.

# Kullanıcı ORM örneğini bir SQLite veritabanına kaydetme kod parçası:
engine = create_engine("sqlite:///:memory:")
# Veritabanı tablolarını oluştur (create tables)
Base.metadata.create_all(engine)

# Veritabanıyla etkileşimde bulunmak için kullanılan bir oturum (session) oluştur.
Session = sessionmaker(bind=engine)
session = Session()

# Yeni bir kullanıcı ekle (add a new user)
new_user = User(name="Alice")
session.add(new_user)
session.commit()

# Kullanıcıyı SQLite kullanıcılar tablosundan sorgulama (query a user):
user = session.query(User).first()
if user:
    print(f"Kullanıcı ID: {user.id}")
    print(f"Kullanıcı adı: {user.name}")

# Tüm scripti ve nasıl çalıştırılacağını GitHub deposunda code_snippets/03_orm.py adresinde bulabilirsiniz.
```

Kod açıklamaları:
1. `from sqlalchemy import Column, Integer, String, create_engine`: SQLAlchemy kütüphanesinden gerekli bileşenleri içe aktarır.
   - `Column`: Veritabanı tablosundaki bir sütunu temsil eder.
   - `Integer` ve `String`: Sütun veri tipleridir.
   - `create_engine`: Veritabanı bağlantısını oluşturur.

2. `Base = declarative_base()`: Tanımlayıcı temel sınıfını oluşturur. Bu, ORM sınıflarını tanımlamak için kullanılır.

3. `class User(Base)`: Kullanıcı ORM sınıfını tanımlar. Bu sınıf, veritabanındaki "users" tablosuna karşılık gelir.

4. `engine = create_engine("sqlite:///:memory:")`: Bellekte bir SQLite veritabanı oluşturur.

5. `Base.metadata.create_all(engine)`: Tanımlanan ORM sınıflarına göre veritabanında tabloları oluşturur.

6. `Session = sessionmaker(bind=engine)`: Veritabanıyla etkileşimde bulunmak için bir oturum (session) sınıfı oluşturur.

7. `new_user = User(name="Alice")`: Yeni bir Kullanıcı örneği oluşturur.

8. `session.add(new_user)` ve `session.commit()`: Yeni kullanıcıyı veritabanına ekler ve işlemi kaydeder.

9. `user = session.query(User).first()`: Veritabanından ilk Kullanıcı kaydını sorgular.

ODM deseniyse (Object Document Mapping), ORM'e son derece benzer, ancak SQL veritabanları ve tabloları yerine NoSQL veritabanları (MongoDB gibi) ve yapılandırılmamış koleksiyonlarla çalışır. NoSQL veritabanlarıyla çalıştığımızdan, veri yapısı koleksiyonlar etrafında merkezlenir ve bu koleksiyonlar tablolardaki satırlar yerine JSON benzeri belgeleri saklar. Sonuç olarak, ODM, belge tabanlı NoSQL veritabanlarıyla çalışmayı basitleştirir ve nesne yönelimli kodu JSON benzeri belgelere eşler. ODM'nin nasıl çalıştığını tam olarak anlamak için MongoDB üzerine hafif bir ODM modülü uygulayacağız.

İngilizce teknik terimlerin parantez içinde Türkçe karşılıkları:
- ORM: Nesne-İlişkisel Eşleme (Object-Relational Mapping)
- ODM: Nesne-Belge Eşleme (Object Document Mapping)
- CRUD: Oluştur, Okuma, Güncelle, Sil (Create, Read, Update, Delete)
- SQL: Yapılandırılmış Sorgu Dili (Structured Query Language)
- NoSQL: Yalnızca sorgulama dili olmayan veritabanları (Not Only SQL)
- JSON: JavaScript Nesne Gösterimi (JavaScript Object Notation)

---

