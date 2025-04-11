#RAG Besleme Hattı (RAG Ingestion Pipeline)

RAG besleme hattı, çeşitli veri kaynaklarından (örneğin, veri ambarı, veri gölü, web sayfaları vb.) ham belgeleri çıkarır. Daha sonra belgeleri temizler, parçalar (daha küçük bölümlere ayırır) ve gömülü hale getirir (embedder). Sonunda, gömülü parçaları bir vektör veritabanına (veya diğer benzer vektör depolama alanlarına) yükler. Böylece, RAG besleme hattı aşağıdaki modüllere ayrılır:

##Veri Çıkarma Modülü (Data Extraction Module)
Veri çıkarma modülü, DB'ler, API'ler veya web sayfaları gibi çeşitli kaynaklardan gerekli tüm verileri toplar. Bu modül verilerinize oldukça bağlıdır. Veri ambarınızı sorgulamak kadar kolay veya Wikipedia'yı taramak gibi daha karmaşık olabilir.

##Temizleme Katmanı (Cleaning Layer)
Temizleme katmanı, çıkarılan verilerden istenmeyen karakterleri standart hale getirir ve kaldırır. Örneğin, giriş metninizden geçersiz karakterleri, ASCII olmayan ve kalın ve italik karakterleri kaldırmanız gerekir. Diğer bir popüler temizleme stratejisi, URL'leri yer tutucularla değiştirmektir. Ancak, temizleme stratejinizi veri kaynağınıza ve gömme modelinize bağlı olarak değişecektir.

##Parçalama Modülü (Chunking Module)
Parçalama modülü, temizlenen belgeleri daha küçük olanlara böler. Belgenin içeriğini bir gömme modeline aktarmak istediğimiz için, bunun modelin giriş maksimum boyutunu aşmadığından emin olmak için gereklidir. Ayrıca, anlamsal olarak ilgili belirli bölgeleri ayırmak için parçalama gereklidir. Örneğin, bir kitabın bölümünü parçalarken, en uygun yol benzer paragrafları aynı bölüme veya parçaya gruplamaktır. Bunu yaparak, geri alma sırasında yalnızca gerekli verileri isteme ekleyeceksiniz.

##Gömme Bileşeni (Embedding Component)
Gömme bileşeni, bir gömme modelini kullanarak parçanın içeriğini (metin, resim, ses vb.) alır ve anlamsal değerle dolu yoğun bir vektöre projeler - gömme hakkında daha fazla bilgiyi aşağıda "Gömme nedir?" bölümünde bulabilirsiniz.

##Yükleme Modülü (Loading Module)
Yükleme modülü, gömülü parçaları bir meta veri belgesi ile birlikte alır. Meta veriler, gömülü içerik, parçanın kaynağına olan URL ve içerik web'de yayınlandığında gibi temel bilgileri içerecektir. Gömme, benzer parçaları sorgulamak için bir dizin olarak kullanılırken, meta veriler, istemi artırmak için eklenen bilgilere erişmek için kullanılır.

Bu noktada, ham belgeleri girdi olarak alan, işleyen ve bir vektör veritabanını dolduran bir RAG besleme hattımız var. Bir sonraki adım, vektör deposundan ilgili verileri doğru bir şekilde almaktır.

**Kodlar:**
```markdown
# RAG Besleme Hattı
### Veri Çıkarma Modülü
- Veri kaynaklarından ham belgeleri çıkarır (örneğin, veri ambarı, veri gölü, web sayfaları vb.)

### Temizleme Katmanı
- Çıkarılan verilerden istenmeyen karakterleri standart hale getirir ve kaldırır

### Parçalama Modülü
- Temizlenen belgeleri daha küçük olanlara böler

### Gömme Bileşeni
- Parçanın içeriğini (metin, resim, ses vb.) alır ve anlamsal değerle dolu yoğun bir vektöre projeler

### Yükleme Modülü
- Gömülü parçaları bir meta veri belgesi ile birlikte alır
```

**Kodların Ayrıntılı Açıklaması:**

1. `# RAG Besleme Hattı`: Başlık, RAG besleme hattının ne olduğunu belirtir.
2. `### Veri Çıkarma Modülü`: Veri çıkarma modülünün başlığı.
   - `- Veri kaynaklarından ham belgeleri çıkarır (örneğin, veri ambarı, veri gölü, web sayfaları vb.)`: Veri çıkarma modülünün işlevini açıklar.

3. `### Temizleme Katmanı`: Temizleme katmanının başlığı.
   - `- Çıkarılan verilerden istenmeyen karakterleri standart hale getirir ve kaldırır`: Temizleme katmanının işlevini açıklar.

4. `### Parçalama Modülü`: Parçalama modülünün başlığı.
   - `- Temizlenen belgeleri daha küçük olanlara böler`: Parçalama modülünün işlevini açıklar.

5. `### Gömme Bileşeni`: Gömme bileşeninin başlığı.
   - `- Parçanın içeriğini (metin, resim, ses vb.) alır ve anlamsal değerle dolu yoğun bir vektöre projeler`: Gömme bileşeninin işlevini açıklar.

6. `### Yükleme Modülü`: Yükleme modülünün başlığı.
   - `- Gömülü parçaları bir meta veri belgesi ile birlikte alır`: Yükleme modülünün işlevini açıklar.

**İngilizce Teknik Terimlerin Türkçeleri ve Açıklamaları Parantez İçinde:**

- RAG (Retrieval-Augmented Generation): Metin oluşturma işlemini iyileştirmek için dış bilgi kaynaklarından yararlanan bir yaklaşım.
- Veri Ambarı (Data Warehouse): İş zekası ve raporlama için kullanılan, çeşitli kaynaklardan toplanmış verilerin depolandığı sistem.
- Veri Gölü (Data Lake): Ham, işlenmemiş verilerin depolandığı bir sistem.
- Gömme Modeli (Embedding Model): Kelimeleri, cümleleri veya belgeleri vektör uzayında temsil eden modeller.
- Vektör Veritabanı (Vector DB): Vektörleri depolamak ve sorgulamak için tasarlanmış veritabanı.
- Meta Veri (Metadata): Veriler hakkında bilgi veren veriler (örneğin, kaynağın URL'si, yayınlanma tarihi).

---

