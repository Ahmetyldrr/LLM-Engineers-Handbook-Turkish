#Makine Öğrenimi Modeli Dağıtım Türleri

Şekil 10.1'de gösterildiği gibi, modelleri sunarken üç temel dağıtım türü arasından seçim yapabilirsiniz: 
Çevrimiçi gerçek zamanlı çıkarım (Online real-time inference) 
Eşzamansız çıkarım (Asynchronous inference) 
Çevrimdışı toplu dönüştürme (Offline batch transform) 
Bir tasarımı diğerine göre seçerken, gecikme (latency), verim (throughput) ve maliyetler (costs) arasında bir denge vardır. 
Verilerin nasıl erişildiğini ve çalıştığınız altyapıyı (infrastructure) dikkate almanız gerekir. 
Dikkate almanız gereken bir diğer kriter, kullanıcının modelle nasıl etkileşime gireceğidir. 
Örneğin, kullanıcı bunu doğrudan bir sohbet robotu (chatbot) gibi kullanacak mı, yoksa bir girdi (input) veya çıktının (output) güvenli olup olmadığını kontrol eden bir sınıflandırıcı (classifier) gibi sisteminiz içinde gizli mi olacak? 
Tahminlerin tazeliğini (freshness of the predictions) de dikkate almanız gerekir. 
Örneğin, modelinizi çevrimdışı toplu modda (offline batch mode) sunmak, kullanım durumunuzda gecikmeli tahminleri tüketmek kabul edilebilir ise uygulanması daha kolay olabilir. 
Aksi takdirde, modelinizi daha fazla altyapı talep eden gerçek zamanlı olarak sunmanız gerekir. 
Ayrıca, uygulamanızın trafiğini (traffic) de dikkate almanız gerekir. 
Kendinize "Uygulama sürekli olarak kullanılacak mı, yoksa trafikte ani artışlar olacak ve sonra düzleşecek mi?" gibi sorular sorun. 
Bunu akılda tutarak, üç ana makine öğrenimi dağıtım türünü inceleyelim.

Şekil 10.1: Çıkarım dağıtım türlerinin üç temel mimarisi

Kodlar yok, ancak metinde geçen bazı teknik terimleri açıklayabilirim:

- `Gecikme (latency)`: Bir işlemin başlangıcından sonuna kadar geçen süre.
- `Verim (throughput)`: Bir sistemin birim zamanda işleyebildiği veri miktarı.
- `Maliyetler (costs)`: Bir sistemin çalışması için gereken kaynakların (donanım, enerji, personel vs.) maliyeti.
- `Altyapı (infrastructure)`: Bir sistemin çalışması için gereken donanım, yazılım ve diğer kaynaklar.
- `Çevrimiçi gerçek zamanlı çıkarım (Online real-time inference)`: Makine öğrenimi modelinin gerçek zamanlı olarak veri işleyerek tahminler yapması.
- `Eşzamansız çıkarım (Asynchronous inference)`: Makine öğrenimi modelinin veri işlemeyi ve tahmin yapmayı eşzamansız olarak gerçekleştirmesi.
- `Çevrimdışı toplu dönüştürme (Offline batch transform)`: Makine öğrenimi modelinin büyük miktarda veriyi toplu olarak işleyerek tahminler yapması.
- `Tahminlerin tazeliği (freshness of the predictions)`: Makine öğrenimi modelinin yaptığı tahminlerin ne kadar güncel olduğu.
- `Sınıflandırıcı (classifier)`: Makine öğrenimi modelinin girdileri belirli kategorilere ayırmaya çalıştığı bir tür model.
- `Trafik (traffic)`: Bir uygulamanın veya sistemin işlediği veri miktarı ve kullanıcı aktivitesi.

---

