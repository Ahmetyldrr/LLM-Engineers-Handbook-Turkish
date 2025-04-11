#Qdrant: Vector Database Seçimi
Qdrant (https://qdrant.tech/), en popüler, sağlam ve özellik açısından zengin vektör veritabanlarından (vector database) biridir. Küçük MVP'miz için neredeyse herhangi bir vektör veritabanı kullanabilirdik, ancak hafif ve endüstride uzun yıllar kullanılmaya devam edecek bir şey seçmek istedik. Qdrant'ı, GenAI kullanılabilirliği için işlendikten ve dönüştürüldükten sonra MongoDB'den gelen verileri depolamak için kullanacağız. Qdrant, X (eski adıyla Twitter), Disney, Microsoft, Discord ve Johnson & Johnson gibi büyük oyuncular tarafından kullanılmaktadır. Bu nedenle, Qdrant'ın vektör veritabanı alanında uzun süre kalması son derece muhtemeldir. Kitabı yazarken, diğer popüler seçenekler Milvus, Redis, Weaviate, Pinecone, Chroma ve pgvector (vektör indeksleri için bir PostgreSQL eklentisi) idi. Qdrant'ın RPS (Request Per Second), gecikme (latency) ve indeksleme zamanı (index time) arasında en iyi dengeyi sunduğunu tespit ettik, bu da onu birçok üretken yapay zeka uygulaması için sağlam bir seçim haline getirmektedir. Tüm vektör veritabanlarını ayrıntılı olarak karşılaştırmak başlı başına bir bölüm olabilir. Bunu burada yapmak istemiyoruz. Yine de merak ediyorsanız, Superlinked tarafından https://superlinked.com/vector-db-comparison adresinde bulunan Vector DB Comparison kaynağını kontrol edebilirsiniz, bu kaynak üst vektör veritabanlarını lisans ve yayın yılından veritabanı özelliklerine, embedding modellerine ve desteklenen çerçevelere kadar her şey açısından karşılaştırmaktadır.

İngilizce teknik terimlerin Türkçe karşılıkları parantez içinde eklenmiştir:
- vector database (vektör veritabanı)
- RPS (Request Per Second - Saniye Başına İstek)
- latency (gecikme)
- index time (indeksleme zamanı)
- embedding models (embedding modelleri)

Kod bulunmamaktadır, dolayısıyla kod açıklaması da bulunmamaktadır. Paragraf doğrudan çevrilmiştir. 

Eğer kod olsaydı, kodun altında satır satır ayrıntılı açıklama aşağıdaki gibi olacaktı:
```python
# Örnek kod
def örnek_fonksiyon():
    pass
```
- `def örnek_fonksiyon():` : Bu satır, `örnek_fonksiyon` adında bir fonksiyon tanımlar.
- `pass` : Bu satır, fonksiyonun gövdesini temsil eder, herhangi bir işlem yapmaz.

Ancak bu metinde kod bulunmamaktadır.

---

