#MongoDB ve NoSQL Veritabanı
MongoDB, günümüzün en popüler, sağlam, hızlı ve özellik bakımından zengin NoSQL veritabanlarından (NoSQL databases) biridir. Çoğu bulut ekosistemiyle (cloud ecosystems) iyi entegre olur, örneğin AWS, Google Cloud, Azure ve Databricks gibi. Bu nedenle, MongoDB'yi NoSQL veritabanımız olarak kullanmak hiç tereddüt etmediğimiz bir seçimdi. Bu kitabı yazdığımızda, MongoDB büyük oyuncular (big players) tarafından kullanılıyordu, örneğin Novo Nordisk, Delivery Hero, Okta ve Volvo gibi. Bu yaygın kullanım, MongoDB'nin uzun süre lider bir NoSQL veritabanı olarak kalacağını gösteriyor. Ham verileri (raw data) işleme sokmadan önce internetten topladığımız verileri depolamak için NoSQL veritabanı olarak MongoDB kullanıyoruz ve daha sonra bu verileri vektör veritabanına (vector database) aktarıyoruz. Yapısal olmayan metin verileriyle (unstructured text data) çalıştığımız için, NoSQL veritabanının esnekliği (flexibility) tam bir uyum sağlıyor.

İngilizce paragraf:
```
MongoDB is one of today’s most popular, robust, fast, and feature-rich NoSQL databases. It integrates well with most cloud ecosystems, such as AWS, Google Cloud, Azure, and Databricks. Thus, using MongoDB as our NoSQL database was a no-brainer. When we wrote this book, MongoDB was used by big players such as Novo Nordisk, Delivery Hero, Okta, and Volvo. This widespread adoption suggests that MongoDB will remain a leading NoSQL database for a long time. We use MongoDB as a NoSQL database to store the raw data we collect from the internet before processing it and pushing it into the vector database. As we work with unstructured text data, the flexibility of the NoSQL database fits like a charm.
```

Kodların açıklamaları:
Yukarıdaki metinde kod bulunmamaktadır. Sadece bir paragraf çevirisi yapılmıştır.

Eğer kodları ayrıntılı olarak açıklayacak olsaydık, örneğin MongoDB ile ilgili bir kod örneği verebilir ve bunu açıklayabilirdik. Aşağıda basit bir MongoDB bağlantı kodu örneği ve açıklaması yer almaktadır:

Örnek Kod:
```python
from pymongo import MongoClient

# MongoDB'ye bağlan
client = MongoClient('mongodb://localhost:27017/')

# Veritabanı seç
db = client['mydatabase']

# Koleksiyon seç
collection = db['mycollection']

# Veri ekle
def insert_data(data):
    result = collection.insert_one(data)
    return result.inserted_id

# Veri ekleme örneği
data = {'name': 'John', 'age': 30}
inserted_id = insert_data(data)
print(f"Inserted ID: {inserted_id}")

# Bağlantıyı kapat
client.close()
```

Kod Açıklaması:
1. `from pymongo import MongoClient`: PyMongo kütüphanesinden MongoClient sınıfını içe aktarır. Bu, MongoDB'ye bağlanmak için kullanılır.
2. `client = MongoClient('mongodb://localhost:27017/')`: Yerel makinede çalışan MongoDB'ye bağlanır. Bağlantı dizesi (`mongodb://localhost:27017/`) MongoDB'nin varsayılan bağlantı adresini ve portunu belirtir.
3. `db = client['mydatabase']`: 'mydatabase' adında bir veritabanı seçer veya eğer böyle bir veritabanı yoksa yaratır.
4. `collection = db['mycollection']`: Seçili veritabanında 'mycollection' adlı bir koleksiyon seçer veya yaratır. Koleksiyon, SQL veritabanlarındaki tablolara benzer.
5. `insert_data` fonksiyonu: Bu fonksiyon, verilen veriyi (`data`) seçili koleksiyona ekler. `insert_one` methodu, tek bir belgeyi (`document`) koleksiyona eklemek için kullanılır.
6. `data = {'name': 'John', 'age': 30}`: Eklenecek örnek veriyi tanımlar. Bu, bir Python sözlüğüdür (`dictionary`) ve MongoDB'de bir belgeye (`document`) karşılık gelir.
7. `inserted_id = insert_data(data)`: Tanımlanan veriyi koleksiyona ekler ve eklenen belgenin ID'sini döndürür.
8. `print(f"Inserted ID: {inserted_id}")`: Eklenen belgenin ID'sini yazdırır.
9. `client.close()`: MongoDB'ye olan bağlantıyı kapatır. Bu, kaynakların serbest bırakılması için önemlidir.

---

