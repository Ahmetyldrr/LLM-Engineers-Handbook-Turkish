# Veritabanlarında Vektör Arama Nasıl Çalışır?

Genellikle bir veritabanında (DB) nasıl arama yaptığınızı düşünün. Belirli bir şey yazarsınız ve sistem tam eşleşmeyi verir. Geleneksel veritabanları böyle çalışır. Vektör veritabanları (Vector DBs) farklıdır. Tam eşleşmeler yerine, sorgu vektörünün (query vector) en yakın komşularını (closest neighbors) ararız. İçten içe, bir vektör veritabanı yaklaşık en yakın komşu (Approximate Nearest Neighbor - ANN) algoritmaları kullanarak bu yakın komşuları bulur. ANN algoritmaları belirli bir arama için en iyi eşleşmeleri döndürmezken, standart en yakın komşu algoritmaları pratikte çalışmak için çok yavaştır. Ayrıca, belirli bir girdi sorgusu için yalnızca üst eşleşmelerin yaklaşık değerlerini kullanmanın yeterince iyi çalıştığı deneysel olarak gösterilmiştir. Bu nedenle, doğruluk (accuracy) ve gecikme (latency) arasındaki ödünleşim nihayetinde ANN algoritmalarını destekler.

Bu, bir vektör veritabanının (Vector DB) tipik iş akışının (workflow) bir örneğidir:
 
### Vektörleri İndeksleme (Indexing Vectors)
Vektörler, yüksek boyutlu veri (high-dimensional data) için optimize edilmiş veri yapıları kullanılarak indekslenir. Yaygın indeksleme teknikleri arasında hiyerarşik gezilebilir küçük dünya (Hierarchical Navigable Small World - HNSW), rastgele izdüşüm (random projection), ürün niceleme (Product Quantization - PQ) ve yerel duyarlı hashing (Locality-Sensitive Hashing - LSH) bulunur.

### Benzerlik için Sorgulama (Querying for Similarity)
Bir arama sırasında, veritabanı girdi vektörüne en benzer olanları bulmak için indekslenmiş vektörleri sorgular. Bu işlem, kosinüs benzerliği (cosine similarity), Euclidean uzaklığı (Euclidean distance) veya nokta çarpımı (dot product) gibi benzerlik ölçütlerine (similarity measures) dayanarak vektörleri karşılaştırmayı içerir. Her birinin benzersiz avantajları vardır ve farklı kullanım durumları için uygundur.

### Sonuçları Sonradan İşleme (Post-processing Results)
Potansiyel eşleşmeler belirlendikten sonra, sonuçlar doğruluklarını artırmak için sonradan işleme tabi tutulur. Bu adım, kullanıcıya en alakalı vektörlerin döndürülmesini sağlar. Vektör veritabanları, vektör araması öncesi veya sonrası meta verilere (metadata) göre sonuçları filtreleyebilir. Her iki yaklaşımın da performans ve doğruluk açısından ödünleşimleri vardır. Sorgu aynı zamanda meta veri indeksine (metadata index) de bağlıdır (vektör indeksiyle birlikte), bu nedenle filtreleme işlemleri için kullanılan bir meta veri indeksi içerir.

Kodları ayrıntılı olarak açıklamak için bir kod örneği olmadığı için, yukarıdaki metinde geçen teknik terimlerin ve kavramların açıklamalarını yapacağım:

- **Vektör Veritabanı (Vector DB):** Vektör verilerini depolayan ve bu veriler üzerinde benzerlik tabanlı sorgular yapabilen veritabanı türü.
- **Yaklaşık En Yakın Komşu (Approximate Nearest Neighbor - ANN):** Yüksek boyutlu uzaylarda en yakın komşuları yaklaşık olarak bulmak için kullanılan algoritmalar.
- **Hiyerarşik Gezilebilir Küçük Dünya (Hierarchical Navigable Small World - HNSW):** Yüksek boyutlu vektörler için kullanılan bir indeksleme tekniği.
- **Rastgele İzdüşüm (Random Projection):** Yüksek boyutlu verileri daha düşük boyutlu uzaylara izdüşümlemek için kullanılan bir teknik.
- **Ürün Niceleme (Product Quantization - PQ):** Vektörleri sıkıştırmak ve benzerlik arama işlemlerini hızlandırmak için kullanılan bir teknik.
- **Yerel Duyarlı Hashing (Locality-Sensitive Hashing - LSH):** Benzer öğeleri aynı veya benzer hash değerlerine mapping eden bir hashing tekniği.
- **Kosinüs Benzerliği (Cosine Similarity):** İki vektör arasındaki açının kosinüsünü hesaplayarak benzerliklerini ölçen bir metrik.
- **Euclidean Uzaklığı (Euclidean Distance):** İki nokta arasındaki uzaklığı ölçen bir metrik.
- **Nokta Çarpımı (Dot Product):** İki vektörün çarpımını hesaplayan ve vektörler arasındaki ilişkiyi ölçen bir işlem.

İngilizce teknik terimlerin Türkçe karşılıkları parantez içinde verildi. 

```markdown
# Veritabanlarında Vektör Arama Nasıl Çalışır?

Genellikle bir veritabanında (DB) nasıl arama yaptığınızı düşünün. Belirli bir şey yazarsınız ve sistem tam eşleşmeyi verir. Geleneksel veritabanları böyle çalışır. Vektör veritabanları (Vector DBs) farklıdır. Tam eşleşmeler yerine, sorgu vektörünün (query vector) en yakın komşularını (closest neighbors) ararız. İçten içe, bir vektör veritabanı yaklaşık en yakın komşu (Approximate Nearest Neighbor - ANN) algoritmaları kullanarak bu yakın komşuları bulur. ANN algoritmaları belirli bir arama için en iyi eşleşmeleri döndürmezken, standart en yakın komşu algoritmaları pratikte çalışmak için çok yavaştır. Ayrıca, belirli bir girdi sorgusu için yalnızca üst eşleşmelerin yaklaşık değerlerini kullanmanın yeterince iyi çalıştığı deneysel olarak gösterilmiştir. Bu nedenle, doğruluk (accuracy) ve gecikme (latency) arasındaki ödünleşim nihayetinde ANN algoritmalarını destekler.

Bu, bir vektör veritabanının (Vector DB) tipik iş akışının (workflow) bir örneğidir:
 
### Vektörleri İndeksleme (Indexing Vectors)
Vektörler, yüksek boyutlu veri (high-dimensional data) için optimize edilmiş veri yapıları kullanılarak indekslenir. Yaygın indeksleme teknikleri arasında hiyerarşik gezilebilir küçük dünya (Hierarchical Navigable Small World - HNSW), rastgele izdüşüm (random projection), ürün niceleme (Product Quantization - PQ) ve yerel duyarlı hashing (Locality-Sensitive Hashing - LSH) bulunur.

### Benzerlik için Sorgulama (Querying for Similarity)
Bir arama sırasında, veritabanı girdi vektörüne en benzer olanları bulmak için indekslenmiş vektörleri sorgular. Bu işlem, kosinüs benzerliği (cosine similarity), Euclidean uzaklığı (Euclidean distance) veya nokta çarpımı (dot product) gibi benzerlik ölçütlerine (similarity measures) dayanarak vektörleri karşılaştırmayı içerir. Her birinin benzersiz avantajları vardır ve farklı kullanım durumları için uygundur.

### Sonuçları Sonradan İşleme (Post-processing Results)
Potansiyel eşleşmeler belirlendikten sonra, sonuçlar doğruluklarını artırmak için sonradan işleme tabi tutulur. Bu adım, kullanıcıya en alakalı vektörlerin döndürülmesini sağlar. Vektör veritabanları, vektör araması öncesi veya sonrası meta verilere (metadata) göre sonuçları filtreleyebilir. Her iki yaklaşımın da performans ve doğruluk açısından ödünleşimleri vardır. Sorgu aynı zamanda meta veri indeksine (metadata index) de bağlıdır (vektör indeksiyle birlikte), bu nedenle filtreleme işlemleri için kullanılan bir meta veri indeksi içerir.
```

---

