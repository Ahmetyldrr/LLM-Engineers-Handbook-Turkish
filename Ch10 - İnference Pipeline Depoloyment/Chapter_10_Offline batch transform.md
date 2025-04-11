#Batch Transform (Toplu Dönüşüm)

    Toplu dönüşüm, büyük hacimli verileri aynı anda işlemekle ilgilidir, bu işlem ya bir zamanlamaya göre ya da manuel olarak tetiklenir. Bir toplu dönüşüm mimarisinde, Makine Öğrenimi (ML) servisi verileri bir depolama sisteminden çeker, tek bir işlemde işler ve ardından sonuçları depolamaya kaydeder. Depolama sistemi, AWS S3 gibi bir nesne depolama veya GCP BigQuery gibi bir veri ambarı olarak uygulanabilir. Eşzamansız çıkarım (asynchronous inference) mimarisinden farklı olarak, bir toplu dönüşüm tasarımı, yüksek verim (high throughput) için optimize edilmiştir ve gecikme gereksinimleri (latency requirements) esnektir. Gerçek zamanlı tahminler (real-time predictions) gereksiz olduğunda, bu yaklaşım maliyetleri önemli ölçüde azaltabilir, çünkü büyük partiler halinde veri işleme en ekonomik yöntemdir. Ayrıca, toplu dönüşüm mimarisi, bir modeli sunmanın en basit yoludur ve geliştirme süresini hızlandırır. İstemci (client), sonuçlarını doğrudan veri depolamasından çeker ve ML servisi ile etkileşimini ayırır (decoupling). Bu yaklaşımı kullanarak, istemci ML servisinin girdisini işlemesini beklemek zorunda kalmaz, ancak aynı zamanda yeni sonuçlar için herhangi bir zamanda talepte bulunma esnekliğine sahip değildir. Sonuçların depolandığı veri depolamasını, istemcinin ihtiyaç duyduğunu alabileceği büyük bir önbellek (cache) olarak görebilirsiniz. Uygulamanızı daha duyarlı hale getirmek istiyorsanız, istemci işlem tamamlandığında bilgilendirilebilir ve sonuçları alabilir. Ne yazık ki, bu yaklaşım her zaman tahminlerin hesaplandığı zaman ile tüketildiği zaman arasında bir gecikme yaratacaktır. Bu nedenle, tüm uygulamalar bu tasarım seçiminden yararlanamaz. Örneğin, bir video akışı uygulaması için bir öneri sistemi (recommender system) uygularsak, önerilen filmler ve TV şovları için bir günlük gecikme işe yarayabilir çünkü bu ürünleri yüksek bir sıklıkta tüketmezsiniz. Ancak, bir sosyal medya platformu için bir öneri sistemi yaparsanız, bir gün veya hatta bir saat gecikme kabul edilemez, çünkü kullanıcıya sürekli taze içerik sağlamak istersiniz. Toplu dönüşüm, veri analizi (data analytics) veya periyodik raporlama (periodic reporting) gibi yüksek verimin gerektiği senaryolarda parlar. Ancak, yüksek gecikmesi nedeniyle gerçek zamanlı uygulamalar için uygun değildir ve büyük veri kümelerini etkili bir şekilde yönetmek için dikkatli planlama ve zamanlama gerektirir. Bu nedenle, çevrimdışı (offline) bir sunum yöntemidir.

    #Kodlar Yok, Açıklama Yapılacaktır.

    Paragrafın altında kod bulunmadığından, kodların ayrıntılı açıklaması da yapılmayacaktır. Ancak, metinde geçen teknik terimlerin açıklamaları aşağıda verilmiştir:

    - **Batch Transform**: Toplu dönüşüm, büyük hacimli verileri aynı anda işleme sürecidir.
    - **ML Service**: Makine Öğrenimi servisi, makine öğrenimi modellerini çalıştıran ve tahminler yapan bir sistemdir.
    - **Object Storage**: Nesne depolama, verileri nesne olarak depolayan bir depolama sistemidir (örneğin, AWS S3).
    - **Data Warehouse**: Veri ambarı, verileri analiz etmek için kullanılan bir sistemdir (örneğin, GCP BigQuery).
    - **Asynchronous Inference**: Eşzamansız çıkarım, bir makine öğrenimi modelinin girdisini işleyip sonucu döndürmesi işlemidir, ancak bu işlem eşzamansız olarak yapılır.
    - **High Throughput**: Yüksek verim, bir sistemin birim zamanda işleyebildiği veri miktarını ifade eder.
    - **Latency Requirements**: Gecikme gereksinimleri, bir sistemin yanıt vermesi için izin verilen maksimum süreyi ifade eder.
    - **Real-time Predictions**: Gerçek zamanlı tahminler, makine öğrenimi modellerinin anlık girdilere yanıt vermesi işlemidir.
    - **Decoupling**: Ayırma, bir sistemin bileşenlerini birbirinden bağımsız hale getirmeyi ifade eder.
    - **Cache**: Önbellek, sık erişilen verileri geçici olarak depolayan bir sistemdir.
    - **Recommender System**: Öneri sistemi, kullanıcılara önerilerde bulunan bir makine öğrenimi modelidir.

    İngilizce paragrafın birebir Türkçe çevirisi yukarıda verilmiştir. Teknik terimlerin Türkçe karşılıkları parantez içinde eklenmiştir.

---

