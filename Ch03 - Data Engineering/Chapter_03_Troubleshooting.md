#MongoDB Veritabanındaki Ham Veri (Raw Data) 
MongoDB veritabanında saklanan ham veri (raw data), gelecekteki tüm adımların merkezinde yer alır. Bu nedenle, eğer tarayıcılarla (crawlers) ilgili herhangi bir sorun nedeniyle bu bölümdeki kodu başarıyla çalıştıramadıysanız, bu bölüm, ilerlemenize izin vermek için potansiyel sorunları çözmek üzere çözümler sunar.

**İngilizce Paragraf:**
The raw data stored in the MongoDB database is central to all future steps. Thus, if you haven’t successfully run the code from this chapter due to any issues with the crawlers, this section provides solutions for fixing potential issues to allow you to move forward.

**Kod Açıklaması:**
Paragraf içinde kod bulunmamaktadır, ancak başlık ve paragrafın kendisi bir kod bloğu içinde değil, metin olarak verilmiştir. 

Eğer kod bloğu olsaydı, her bir satırın açıklaması ayrı ayrı yapılırdı. Örneğin, eğer bir kod bloğu 
```python
# MongoDB bağlantısı için gerekli kütüphane import edilir
import pymongo

# MongoDB'ye bağlanmak için bir client oluşturulur
client = pymongo.MongoClient("mongodb://localhost:27017/")

# Veritabanı seçimi yapılır
db = client["veritabani_adi"]
```
şeklinde olsaydı, açıklamalar şu şekilde olurdu:

1. `import pymongo` : MongoDB bağlantısı için gerekli olan `pymongo` kütüphanesini import eder. Bu kütüphane, Python ile MongoDB arasında etkileşim kurulmasını sağlar.
2. `client = pymongo.MongoClient("mongodb://localhost:27017/")` : Yerel makinede çalışan MongoDB'ye bağlanmak için bir client oluşturur. Burada `"mongodb://localhost:27017/"` bağlantı stringidir (connection string).
3. `db = client["veritabani_adi"]` : MongoDB client'ı üzerinden "veritabani_adi" adındaki veritabanını seçer. Bu, daha sonra veri okumak veya yazmak için kullanılır.

---

