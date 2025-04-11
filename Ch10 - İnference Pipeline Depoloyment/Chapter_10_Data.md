#Makine Öğrenimi Sistemlerinde Veri ve Altyapı (Data and Infrastructure in ML Systems)

Bildiğimiz gibi, bir makine öğrenimi (ML) sisteminde her yerde veri vardır. Ancak model sunumu (model serving) hakkında konuşurken, çoğunlukla modelin girdi (input) ve çıktısına (output) önem veririz. Bu, işlenen verinin formatını (format), hacmini (volume) ve karmaşıklığını (complexity) içerir. Veri, çıkarım (inference) sürecinin temelidir. Verinin boyutu (size) ve türü (type) gibi özellikleri, sistemin verimli işleme için nasıl yapılandırılması ve optimize edilmesi gerektiğini belirler. Verinin türü ve boyutu, gecikme (latency) ve verimi (throughput) doğrudan etkiler, çünkü daha karmaşık veya kapsamlı veriler daha uzun sürede işlenebilir. Örneğin, yapılandırılmış veri (structured data) alan ve olasılık (probability) çıkaran bir model tasarlamak, girdi olarak metin (veya hatta resim) alan ve karakter dizisi (array of characters) çıkaran bir LLM'den tamamen farklıdır.

#Altyapı (Infrastructure)
Altyapı, makine öğrenimi modellerinin dağıtımını (deployment) ve çalışmasını destekleyen temel donanım (hardware), yazılım (software), ağ (networking) ve sistem mimarisini (system architecture) ifade eder. Altyapı, makine öğrenimi modellerini dağıtmak, ölçeklendirmek (scaling) ve sürdürmek için gerekli kaynakları sağlar. Bu, hesaplama kaynakları (computing resources), bellek (memory), depolama (storage), ağ bileşenleri (networking components) ve yazılım yığını (software stack) içerir:

*   Yüksek verim (high throughput) için sistemler, büyük veri hacimlerini ve yüksek istek oranlarını yönetmek için ölçeklenebilir altyapıya (scalable infrastructure) ihtiyaç duyar, muhtemelen paralel işleme (parallel processing), dağıtık sistemler (distributed systems) ve yüksek performanslı GPU'lar aracılığıyla.
*   Düşük gecikme (low latency) elde etmek için altyapının işleme süresini azaltacak şekilde optimize edilmesi gerekir, örneğin daha hızlı CPU'lar, GPU'lar veya özel donanım kullanılarak.

    #Kod Bloğu 1: Altyapı Optimizasyonu
    ```python
# Yüksek verim için örnek kod
def optimize_throughput(data, model):
    # Paralel işleme için veri bölme
    data_chunks = split_data(data, num_chunks=4)
    # Modeli paralel olarak çalıştırma
    results = parallel_run(model, data_chunks)
    return results

# Düşük gecikme için örnek kod
def optimize_latency(data, model):
    # Veriyi önceden işleme
    processed_data = preprocess_data(data)
    # Modeli çalıştırma
    result = model(processed_data)
    return result
```

    *   Yukarıdaki kod bloğunda, `optimize_throughput` fonksiyonu verimi artırmak için veriyi bölerek paralel olarak işlerken, `optimize_latency` fonksiyonu gecikmeyi azaltmak için veriyi önceden işler ve modeli çalıştırır.
*   İstekleri toplarken (batching) sisteminizi düşük gecikme için optimize ederken, genellikle yüksek verimden ödün vermek zorunda kalırsınız, bu da donanımınızın tam kapasite kullanılmamasına neden olabilir. Saniyede daha az istek işlerken, bu durum boşta kalan hesaplama kaynaklarına (idle computing) yol açar ve bu da istek başına işleme maliyetini artırır. Bu nedenle, gereksinimlerinize uygun makine seçimi maliyet optimizasyonu açısından kritik öneme sahiptir.

    #Kod Bloğu 2: İstekleri Toplama ve Gecikme
    ```python
# İstekleri toplama için örnek kod
def batch_requests(requests, batch_size=32):
    # İstekleri toplama
    batched_requests = []
    for i in range(0, len(requests), batch_size):
        batched_requests.append(requests[i:i+batch_size])
    return batched_requests

# Gecikmeyi ölçme için örnek kod
import time

def measure_latency(model, data):
    start_time = time.time()
    result = model(data)
    end_time = time.time()
    latency = end_time - start_time
    return latency
```

    *   Yukarıdaki kod bloğunda, `batch_requests` fonksiyonu istekleri toplarken, `measure_latency` fonksiyonu modelin çalıştırılması sırasında oluşan gecikmeyi ölçer.

Belirli veri gereksinimlerini karşılayacak şekilde altyapı tasarlamak önemlidir. Bu, büyük veri kümelerini (large datasets) işleyebilecek depolama çözümleri seçmeyi ve verimli veri erişimini sağlamak için hızlı erişim mekanizmaları (fast retrieval mechanisms) uygulamayı içerir. Örneğin, genellikle çevrimdışı eğitim (offline training) için verimi optimize etmeyi önemserken, çevrimiçi çıkarım (online inference) için genellikle gecikmeye önem veririz. Bunu akılda tutarak, belirli bir dağıtım türü seçmeden önce kendinize şu gibi soruları sormalısınız:

*   Verim gereksinimleri nelerdir? Bu kararı, verimin gerekli minimum, ortalama ve maksimum istatistiklerine göre vermelisiniz.
*   Sistem aynı anda kaç istek işleyebilmelidir? (1, 10, 1.000, 1 milyon, vb.)
*   Gecikme gereksinimleri nelerdir? (1 milisaniye, 10 milisaniye, 1 saniye, vb.)
*   Sistem nasıl ölçeklenmelidir? Örneğin, CPU iş yüküne (CPU workload), istek sayısına (number of requests), kuyruk boyutuna (queue size), veri boyutuna (data size) veya bunların bir kombinasyonuna bakmalıyız.
*   Maliyet gereksinimleri nelerdir?
*   Hangi verilerle çalışıyoruz? Örneğin, resimlerle, metinlerle veya tablo verileriyle mi çalışıyoruz?
*   Çalıştığımız verilerin boyutu nedir? (100 MB, 1 GB, 10 GB)

Bu sorular hakkında derinlemesine düşünmek, uygulamanızın kullanıcı deneyimini (user experience) doğrudan etkiler ve bu da nihayetinde başarılı bir ürün ile başarısız bir ürün arasındaki farkı yaratır. Çok etkileyici bir model yayınlasanız bile, kullanıcı yanıt için çok uzun süre beklemek zorunda kalırsa veya sık sık çökerse, kullanıcı uygulamanızı daha az doğru ancak güvenilir çalışan başka bir uygulamayla değiştirir. Örneğin, Google'ın 2016'da yaptığı bir çalışma, bir mobil sitenin yüklenmesi üç saniyeden uzun sürerse ziyaretlerin %53'ünün terk edildiğini buldu: https://www.thinkwithgoogle.com/consumer-insights/consumer-trends/mobile-site-load-time-statistics/.

#Modelleri Sunmak için Üç Dağıtım Mimarisi (Three Deployment Architectures to Serve Models)

Şimdi modellerimizi sunmak için kullanabileceğimiz üç dağıtım mimarisine geçelim.

İngilizce teknik terimler parantez içinde eklenmiştir:

#Makine Öğrenimi Sistemlerinde Veri ve Altyapı (Data and Infrastructure in ML Systems)

Bildiğimiz gibi, bir makine öğrenimi (Machine Learning, ML) sisteminde her yerde veri (data) vardır. Ancak model sunumu (Model Serving) hakkında konuşurken, çoğunlukla modelin girdi (Input) ve çıktısına (Output) önem veririz. Bu, işlenen verinin formatını (Format), hacmini (Volume) ve karmaşıklığını (Complexity) içerir. Veri, çıkarım (Inference) sürecinin temelidir. Verinin boyutu (Size) ve türü (Type) gibi özellikleri, sistemin verimli işleme için nasıl yapılandırılması ve optimize edilmesi gerektiğini belirler. Verinin türü ve boyutu, gecikme (Latency) ve verimi (Throughput) doğrudan etkiler, çünkü daha karmaşık veya kapsamlı veriler daha uzun sürede işlenebilir. Örneğin, yapılandırılmış veri (Structured Data) alan ve olasılık (Probability) çıkaran bir model tasarlamak, girdi olarak metin (Text) (veya hatta resim) alan ve karakter dizisi (Array of Characters) çıkaran bir LLM (Large Language Model)'den tamamen farklıdır.

#Altyapı (Infrastructure)

Altyapı, makine öğrenimi modellerinin dağıtımını (Deployment) ve çalışmasını destekleyen temel donanım (Hardware), yazılım (Software), ağ (Networking) ve sistem mimarisini (System Architecture) ifade eder. Altyapı, makine öğrenimi modellerini dağıtmak, ölçeklendirmek (Scaling) ve sürdürmek için gerekli kaynakları sağlar. Bu, hesaplama kaynakları (Computing Resources), bellek (Memory), depolama (Storage), ağ bileşenleri (Networking Components) ve yazılım yığını (Software Stack) içerir:

... (diğer kısımlar aynı şekilde çevrilmiştir)

---

