#Throughput ve Latency

Throughput, bir sistemin belirli bir süre içinde işleyebileceği çıkarım (inference) isteklerinin sayısını ifade eder. Genellikle saniye başına istek (requests per second (RPS)) olarak ölçülür. Throughput, birçok istek beklediğinizde Makine Öğrenimi (Machine Learning (ML)) modellerini dağıtırken çok önemlidir. Sistemin birçok isteği verimli bir şekilde işleyebilmesini ve darboğaz haline gelmemesini sağlar. Yüksek throughput genellikle ölçeklenebilir ve sağlam altyapı gerektirir, örneğin birden fazla yüksek uçlu Grafik İşleme Birimi (Graphics Processing Unit (GPU)) olan makineler veya kümeler.

Latency, bir sistemin tek bir çıkarım isteğini aldıktan sonra sonucu döndürünceye kadar geçen süredir. Latency, canlı kullanıcı etkileşimleri, dolandırıcılık tespiti veya anında geri bildirim gerektiren herhangi bir sistem gibi hızlı yanıt sürelerinin önemli olduğu gerçek zamanlı uygulamalarda kritik öneme sahiptir. Örneğin, OpenAI'ın API'sinin ortalama latency değeri, bir kullanıcı bir istek gönderdiğinde ve hizmet bir sonuç sağladığında erişilebilen ortalama yanıt zamanıdır. Latency, ağ G/Ç (Input/Output (I/O)), serileştirme ve serileştirmeyi kaldırma (serialization and deserialization) ve Büyük Dil Modeli'nin (Large Language Model (LLM)) çıkarım süresinin toplamıdır. Bu arada, throughput, API'nin saniyede işlediği ve sunduğu ortalama istek sayısıdır.

Düşük latency sistemleri, daha hızlı işlemciler, daha düşük ağ latency'si ve muhtemelen veri seyahat mesafesini azaltmak için kenar bilişim (edge computing) gibi optimize edilmiş ve genellikle daha maliyetli altyapı gerektirir. Daha düşük latency, hizmetin birden fazla sorguyu paralel olarak başarıyla işlediği durumlarda daha yüksek throughput'a karşılık gelir. Örneğin, hizmet 100 ms'de istekleri işlerse, bu saniyede 10 isteklik bir throughput'a karşılık gelir. Latency 10 ms'ye düşerse, throughput 100 istek/saniye'ye yükselir. Ancak işleri karmaşık hale getirmek için, çoğu ML uygulaması, aynı anda birden fazla veri örneğini modele iletmek için bir toplu işleme (batching) stratejisi benimser. Bu durumda, daha düşük latency daha düşük throughput'a karşılık gelebilir; başka bir deyişle, daha yüksek latency daha yüksek throughput'a karşılık gelir. Örneğin, 20 toplu istek 100 ms'de işlenirse, latency 100 ms'dir, throughput ise 200 istek/saniye'dir. 60 istek 200 ms'de işlenirse, latency 200 ms'dir, throughput ise 300 istek/saniye'ye yükselir. Bu nedenle, hizmet verme sırasında istekleri toplu olarak işlerken bile, iyi bir kullanıcı deneyimi için kabul edilen minimum latency'yi dikkate almak önemlidir.

Kod bulunmamaktadır, ancak metinde geçen teknik terimlerin açıklamaları aşağıda verilmiştir:

- **Throughput**: Bir sistemin belirli bir süre içinde işleyebileceği istek sayısı.
- **RPS (Requests Per Second)**: Saniye başına istek sayısı.
- **ML (Machine Learning)**: Makine Öğrenimi, makinelerin veri deneyimlerinden öğrenmelerini sağlayan bir Yapay Zeka (Artificial Intelligence (AI)) alt kümesidir.
- **GPU (Graphics Processing Unit)**: Grafik İşleme Birimi, özellikle grafik işleme için tasarlanmış bir işlemcidir, ancak ML ve diğer paralel işleme görevlerinde de kullanılır.
- **Latency**: Bir sistemin bir istek üzerine yanıt vermesi için geçen süre.
- **I/O (Input/Output)**: Giriş/Çıkış işlemleri, bir sistemin dış dünya ile etkileşimde bulunmasını sağlar.
- **Serialization ve Deserialization**: Veri yapılarının veya nesnelerin depolanabilir veya iletilebilir bir formata dönüştürülmesi ve bu formattan geri dönüştürülmesi işlemleridir.
- **LLM (Large Language Model)**: Büyük Dil Modeli, büyük miktarda metin verisi üzerinde eğitilmiş bir dil modelidir.
- **Edge Computing**: Kenar Bilişim, veri işleme ve analizini veri kaynağına yakın bir yerde yapmayı amaçlayan bir bilişim paradigmasıdır.

---

