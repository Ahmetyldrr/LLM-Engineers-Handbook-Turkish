# Verileri Yapılandırmak için Belge Sınıfları (Document Classes) Oluşturma

Veri kategorilerimizi yapılandırmak için üç belge sınıfı (document classes) uygulamak zorunda kaldık. Bu sınıflar, içerik, yazar ve kaynak bağlantısı gibi bir belge için gereksinim duyduğumuz belirli özellikleri tanımlar. Verilerinizi sınıflarda yapılandırmak, sözlükler (dictionaries) yerine en iyi uygulamadır, çünkü her bir öğe için beklediğimiz özellikler daha ayrıntılıdır ve çalıştırma hatalarını azaltır. Örneğin, bir Python sözlüğünden (Python dictionary) bir değere erişirken, onun mevcut olduğundan veya türünün güncel olduğundan asla emin olamayız. Veri öğelerimizi sınıflarla sarmalayarak (wrapping), her bir özelliğin beklendiği gibi olmasını sağlayabiliriz. Pydantic gibi Python paketlerinden yararlanarak, veri kümelerimizde tutarlılığı sağlayan kutudan çıkan tür doğrulamasına (out-of-the-box type validation) sahip oluruz. Böylece, veri kategorilerini aşağıdaki belge sınıfları olarak modelledik, ki bunları kodda daha önceki noktalara kadar zaten kullanmıştık: 
- ArticleDocument sınıfı 
- PostDocument sınıfı 
- RepositoryDocument sınıfı 

Bunlar basit Python veri sınıfları (Python data classes) veya Pydantic modelleri (Pydantic models) değildir. MongoDB veri ambarı (data warehouse) üzerinde okuma ve yazma işlemlerini desteklerler. Kod tekrarını önlemek için tüm belge sınıflarına okuma ve yazma işlevselliğini enjekte etmek amacıyla, nesne-ilişkisel eşleme (Object-Relational Mapping, ORM) desenine dayanan Nesne-Belge Eşleme (Object-Document Mapping, ODM) yazılım desenini kullandık. Bu nedenle, önce ORM'yi keşfedelim, sonra ODM'ye geçelim ve son olarak özel ODM uygulamamıza ve belge sınıflarımıza dalalım.

İngilizce Teknik Terimler Parantez İçinde Eklenmiştir:
# Verileri Yapılandırmak için Belge Sınıfları (Document Classes) Oluşturma

Veri kategorilerimizi yapılandırmak için üç belge sınıfı (document classes) uygulamak zorunda kaldık. Bu sınıflar, içerik (content), yazar (author) ve kaynak bağlantısı (source link) gibi bir belge (document) için gereksinim duyduğumuz belirli özellikleri (attributes) tanımlar. Verilerinizi sınıflarda (classes) yapılandırmak, sözlükler (dictionaries) yerine en iyi uygulamadır (best practice), çünkü her bir öğe (item) için beklediğimiz özellikler (attributes) daha ayrıntılıdır (verbose) ve çalıştırma hatalarını (run errors) azaltır. Örneğin, bir Python sözlüğünden (Python dictionary) bir değere (value) erişirken, onun mevcut (present) olduğundan veya türünün (type) güncel (current) olduğundan asla emin olamayız. Veri öğelerimizi (data items) sınıflarla (classes) sarmalayarak (wrapping), her bir özelliğin (attribute) beklendiği gibi olmasını sağlayabiliriz. Pydantic gibi Python paketlerinden (Python packages) yararlanarak, veri kümelerimizde (datasets) tutarlılığı (consistency) sağlayan kutudan çıkan tür doğrulamasına (out-of-the-box type validation) sahip oluruz. Böylece, veri kategorilerini (data categories) aşağıdaki belge sınıfları (document classes) olarak modelledik, ki bunları kodda (code) daha önceki noktalara kadar zaten kullanmıştık: 
- ArticleDocument sınıfı (ArticleDocument class) 
- PostDocument sınıfı (PostDocument class) 
- RepositoryDocument sınıfı (RepositoryDocument class)

Bunlar basit Python veri sınıfları (Python data classes) veya Pydantic modelleri (Pydantic models) değildir. MongoDB veri ambarı (MongoDB data warehouse) üzerinde okuma (read) ve yazma (write) işlemlerini desteklerler. Kod tekrarını (code repetition) önlemek için tüm belge sınıflarına (document classes) okuma ve yazma işlevselliğini (read-and-write functionality) enjekte etmek amacıyla, nesne-ilişkisel eşleme (Object-Relational Mapping, ORM) desenine dayanan Nesne-Belge Eşleme (Object-Document Mapping, ODM) yazılım desenini (software pattern) kullandık. Bu nedenle, önce ORM'yi (ORM) keşfedelim, sonra ODM'ye (ODM) geçelim ve son olarak özel ODM uygulamamıza (custom ODM implementation) ve belge sınıflarımıza (document classes) dalalım.

Kodlar Ayrıntılı Olarak Açıklanmıştır:

1. `We had to implement three document classes to structure our data categories.`
   - Veri kategorilerimizi yapılandırmak için üç belge sınıfı uygulamak zorunda kaldık.

2. `These classes define the specific attributes we require for a document, such as the content, author, and source link.`
   - Bu sınıflar, içerik, yazar ve kaynak bağlantısı gibi bir belge için gereksinim duyduğumuz belirli özellikleri tanımlar.

3. `It is best practice to structure your data in classes instead of dictionaries, as the attributes we expect for each item are more verbose, reducing run errors.`
   - Verilerinizi sınıflarda yapılandırmak, sözlükler yerine en iyi uygulamadır, çünkü her bir öğe için beklediğimiz özellikler daha ayrıntılıdır ve çalıştırma hatalarını azaltır.

4. `For example, when accessing a value from a Python dictionary, we can never be sure it is present or its type is current.`
   - Örneğin, bir Python sözlüğünden bir değere erişirken, onun mevcut olduğundan veya türünün güncel olduğundan asla emin olamayız.

5. `By wrapping our data items with classes, we can ensure each attribute is as expected.`
   - Veri öğelerimizi sınıflarla sarmalayarak, her bir özelliğin beklendiği gibi olmasını sağlayabiliriz.

6. `By leveraging Python packages such as Pydantic, we have out-of-the-box type validation, which ensures consistency in our datasets.`
   - Pydantic gibi Python paketlerinden yararlanarak, veri kümelerimizde tutarlılığı sağlayan kutudan çıkan tür doğrulamasına sahip oluruz.

7. `Thus, we modeled the data categories as the following document classes, which we already used in the code up until point: ArticleDocument class PostDocument class RepositoryDocument class`
   - Böylece, veri kategorilerini aşağıdaki belge sınıfları olarak modelledik, ki bunları kodda daha önceki noktalara kadar zaten kullanmıştık.

8. `These are not simple Python data classes or Pydantic models.`
   - Bunlar basit Python veri sınıfları veya Pydantic modelleri değildir.

9. `They support read and write operations on top of the MongoDB data warehouse.`
   - MongoDB veri ambarı üzerinde okuma ve yazma işlemlerini desteklerler.

10. `To inject the read-and-write functionality into all the document classes without repeating any code, we used the Object-Document Mapping (ODM) software pattern, which is based on the object-relational mapping ( ORM ) pattern.`
    - Kod tekrarını önlemek için tüm belge sınıflarına okuma ve yazma işlevselliğini enjekte etmek amacıyla, nesne-ilişkisel eşleme desenine dayanan Nesne-Belge Eşleme yazılım desenini kullandık.

11. `Thus, let’s first explore ORM, then move to ODM, and, finally, dig into our custom ODM implementation and document classes.`
    - Bu nedenle, önce ORM'yi keşfedelim, sonra ODM'ye geçelim ve son olarak özel ODM uygulamamıza ve belge sınıflarımıza dalalım.

---

