# Veri Değişikliklerini Yakalama (Change Data Capture)

Bu bölümde birkaç kez vurgulandığı gibi, veriler sürekli olarak değişiyor ve bu da Veritabanları (DBs), veri göllerinin (data lakes), veri ambarlarının (data warehouses) ve özellik depolarının (feature stores) senkronizasyonunu kaybetmesine neden olabilir. Veri Değişikliklerini Yakalama (Change Data Capture - CDC), iki veya daha fazla veri depolama türünü işlem ve G/Ç (I/O) yükü olmadan senkronize tutmanıza olanak tanıyan bir stratejidir. Kaynak Veritabanı'nda (source DB) yapılan herhangi bir CRUD işlemini yakalar ve hedef Veritabanı'nda (target DB) yineler. İsteğe bağlı olarak, çoğaltma arasında ön işleme adımları ekleyebilirsiniz. Senkronizasyon sorunları, bir özellik hattı (feature pipeline) oluştururken de geçerlidir. Temel tasarım seçeneklerinden biri, veri ambarını özellik deposuyla senkronize ederek belirli kullanım durumunuz için yeterince taze veriye sahip olmayı içerir. LLM Twin kullanım durumumuzda basitlik nedeniyle saf bir yaklaşım seçtik. Periyodik olarak veya manuel olarak tetiklenen bir toplu işlem hattı (batch pipeline) uyguladık. Veri ambarından tüm ham verileri okur, toplu olarak işler ve Qdrant vektör Veritabanı'ndaki (Qdrant vector DB) yeni kayıtları ekler veya eski olanları günceller. Bu, küçük sayıda kayıtla çalışırken iyi çalışır, ancak veri ani bir şekilde milyonlarca kayda (veya daha fazlasına) büyüdüğünde ne olur? Veri ambarından bir kayıt silindiğinde ne olur? Bu, özellik deposunda nasıl yansıtılır? Veri ambarından yalnızca yeni veya güncellenmiş öğeleri işlemek istiyorsak, tümünü değil? Neyse ki, CDC modeli tüm bu sorunları çözebilir.

CDC uygularken birden fazla yaklaşım izleyebilirsiniz, ancak hepsi bir itme (push) veya çekme (pull) stratejisi kullanır:
```
# İtme (Push) Stratejisi
Kaynak Veritabanı, itme yaklaşımında birincil sürücüdür. 
Aktif olarak veri değişikliklerini tanımlar ve hedef sistemlere işleme için iletir. 
Bu yöntem, hedefte anlık güncellemeler sağlar, ancak hedef sistemler erişilemediğinde veri kaybı meydana gelebilir. 
Bunu azaltmak için, genellikle bir mesajlaşma sistemi arabellek olarak kullanılır.
```
Kod Açıklaması:
- `İtme (Push) Stratejisi`: Kaynak Veritabanı'nın aktif olarak veri değişikliklerini hedef sistemlere ilettiği yaklaşım.
- `Aktif olarak veri değişikliklerini tanımlar`: Kaynak Veritabanı, veri değişikliklerini belirler.
- `Hedef sistemlere işleme için iletir`: Belirlenen veri değişiklikleri hedef sistemlere iletilir.

```
# Çekme (Pull) Stratejisi
Çekme yöntemi, kaynak Veritabanı'na daha pasif bir rol atar; yalnızca veri değişikliklerini kaydeder. 
Hedef sistemler periyodik olarak bu değişiklikleri ister ve güncellemeleri buna göre işler. 
Bu yaklaşım, kaynak üzerindeki yükü hafifletir, ancak veri yayılımında bir gecikme yaratır. 
Mesajlaşma sistemi, hedef sistem kullanılamadığında veri kaybını önlemek için yine önemlidir.
```
Kod Açıklaması:
- `Çekme (Pull) Stratejisi`: Kaynak Veritabanı'nın veri değişikliklerini kaydettiği ve hedef sistemlerin periyodik olarak bu değişiklikleri istediği yaklaşım.
- `Pasif rol`: Kaynak Veritabanı'nın veri değişikliklerini yalnızca kaydetmesi.
- `Hedef sistemler periyodik olarak bu değişiklikleri ister`: Hedef sistemler, veri değişikliklerini periyodik olarak çeker.

# CDC Teknikleri
Veri değişikliklerini algılamak için farklı yöntemler vardır. 
Bu nedenle, endüstride kullanılan ana CDC modellerini listeleyelim:
- **Tarih-saat tabanlı (Timestamp-based)**: Bu yaklaşım, Veritabanı tablolarına bir değişiklik zamanı sütunu eklemeyi içerir, genellikle `LAST_MODIFIED` veya `LAST_UPDATED` olarak adlandırılır. 
  - `Downstream` sistemler, son kontrollerinden beri güncellenmiş kayıtları tanımlamak için bu sütunu sorgulayabilir. 
  - Basitçe uygulanabilmesine rağmen, bu yöntem değişiklikleri izlemeye sınırlıdır, silmeleri izleyemez ve tüm tabloyu tarama ihtiyacından dolayı performans yükü getirir.
- **Tetikleyici tabanlı (Trigger-based)**: Tetikleyici tabanlı yaklaşım, Veritabanı tetikleyicilerini kullanarak `INSERT`, `UPDATE` veya `DELETE` işlemlerinde ayrı bir tabloda veri değişikliklerini otomatik olarak kaydetmeyi içerir, genellikle olay tablosu (event table) olarak bilinir. 
  - Bu yöntem kapsamlı değişiklik izleme sağlar, ancak her olay için ek yazma işlemleri nedeniyle Veritabanı performansını etkileyebilir.
- **Günlük tabanlı (Log-based)**: Veritabanları, tüm veri değişikliklerini, zaman damgalarıyla birlikte kaydetmek için işlem günlüklerini (transaction logs) tutar. 
  - Öncelikle kurtarma için kullanılan bu günlükler, değişiklikleri hedef sistemlere gerçek zamanlı olarak yaymak için de kullanılabilir. 
  - Bu yaklaşım, kaynak Veritabanı üzerindeki performans etkisini en aza indirir. 
  - Kaynak Veritabanı üzerinde ek işlem yükü oluşturmaması, tüm veri değişikliklerini yakalaması ve şema değişikliği gerektirmemesi büyük avantajlardır. 
  - Ancak, standartlaştırılmış günlük formatlarının olmaması, satıcıya özgü uygulamalar anlamına gelir.

CDC hakkında daha fazla ayrıntı için Confluent'in blogundaki "What is Change Data Capture?" makalesini öneririm: https://www.confluent.io/en-gb/learn/change-data-capture/

Bu CDC tekniklerini akılda tutarak, veri büyüdüğünde veri ambarını ve özellik deposunu daha optimal şekilde senkronize etmek için RAG özellik hattımızda bir çekme tarih-saat tabanlı strateji hızlıca uygulayabiliriz. Uygulama, kaynak Veritabanı'ndaki herhangi bir son güncellenen alanı kontrol etmez; veri ambarından her şeyi çeker. Ancak, endüstride en popüler ve optimal teknik günlük tabanlı olanıdır. Kaynak Veritabanı'na herhangi bir G/Ç yükü eklemez, düşük gecikmeye sahiptir ve tüm CRUD işlemlerini destekler. En büyük dezavantajı, tüm CRUD olaylarını yakalamak için bir kuyruk ve bunları işlemek için bir akış hattı gerektiren geliştirme karmaşıklığıdır. Bu bir LLM kitabı ve veri mühendisliği kitabı olmadığı için işleri basit tutmak istedik, ancak bu tekniklerin var olduğunu bilmek önemlidir ve mevcut uygulamanız uygulama gereksinimlerinize artık uymadığında her zaman yükseltebilirsiniz.

İngilizce teknik terimler parantez içinde eklenmiştir:
- Change Data Capture (CDC) - Veri Değişikliklerini Yakalama
- DBs - Veritabanları
- Data lakes - Veri göllerinin
- Data warehouses - Veri ambarlarının
- Feature stores - Özellik depolarının
- CRUD - Create, Read, Update, Delete (Oluştur, Okuma, Güncelleme, Silme) işlemleri
- I/O - Giriş/Çıkış (Input/Output)
- Timestamp-based - Tarih-saat tabanlı
- Trigger-based - Tetikleyici tabanlı
- Log-based - Günlük tabanlı
- Push - İtme
- Pull - Çekme
- Downstream - Akış aşağı (veri işleme zincirinde sonraki aşamalar)

---

