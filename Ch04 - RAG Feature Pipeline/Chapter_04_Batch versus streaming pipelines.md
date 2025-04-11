# Özellik Boru Hatları: Toplu İş ve Akış (Batch and Streaming)

Özellik boru hatları (Feature Pipelines) uygularken iki ana tasarım seçeneğiniz vardır: toplu iş (batch) ve akış (streaming). Bu nedenle, ikisi arasındaki farkı görmek ve neden LLM Twin kullanım durumumuz için akış mimarisine (streaming architecture) karşı toplu iş mimarisini (batch architecture) seçtiğimizi anlamak önemlidir. Akış boru hatları (streaming pipelines) hakkında özel bir bölüm yazmak mümkündür, bu da toplu iş tasarımına (batch design) göre karmaşıklığını gösterir. Ancak, akış mimarileri (streaming architectures) giderek daha popüler hale geldikçe, uygulamanız için en iyi seçeneği seçmek için nasıl çalıştıklarına dair bir sezgiye sahip olmak gerekir.

Akış uygulamalarının (streaming applications) temel öğeleri, birden fazla istemciden (multiple clients) olayları (events) depolamak için Apache Kafka veya Redpanda gibi dağıtılmış bir olay akışı platformu (distributed event streaming platform) ve olayları işlemek için Apache Flink veya Bytewax gibi bir akış motorudur (streaming engine). Mimarinizi basitleştirmek için, olay akışı platformunuzu (event streaming platform), olayları işlenene kadar depolamak için RabbitMQ gibi kuyruklarla (queues) değiştirebilirsiniz.

Tablo 4.1, toplu iş ve akış boru hatlarını (batch and streaming pipelines), işleme çizelgesi (processing schedule) ve karmaşıklık (complexity) gibi çeşitli kriterlere göre karşılaştırır:

| Aspect | Batch pipeline | Streaming pipeline |
| --- | --- | --- |
| Processing schedule | Verileri düzenli aralıklarla işler (örneğin, her dakika, saatlik, günlük). | Verileri sürekli olarak, minimum gecikmeyle işler. |
| Efficiency | Büyük hacimli verileri daha verimli bir şekilde işler, kaynak tahsisini (resource allocation) ve paralel işlemeyi (parallel processing) optimize eder. | Tek veri noktalarını (single data points) işler, anlık içgörüler (immediate insights) ve güncellemeler sağlar, değişikliklere hızlı yanıt verilmesini sağlar. |
| Processing complexity | Karmaşık veri dönüşümleri (complex data transformations) ve toplama işlemleri (aggregations) gerçekleştirebilir. | Yüksek hızlı veri akışlarını (high-velocity data streams) düşük gecikmeyle işler. |
| Use cases | Anlık veri işleme (immediate data processing) kritik olmayan senaryolar için uygundur. | Gerçek zamanlı analizler (real-time analytics), özellikler (features), izleme (monitoring) ve olay güdümlü mimariler (event-driven architectures) gerektiren uygulamalar için idealdir. |
| System complexity | Akış boru hatlarına (streaming pipelines) kıyasla sistemler genellikle daha basit bir şekilde uygulanır ve korunur. | Düşük gecikmeli işleme (low-latency processing), hata toleransı (fault tolerance) ve ölçeklenebilirlik (scalability) gereksinimleri nedeniyle daha karmaşık bir şekilde uygulanır ve korunur. Araçlar da daha gelişmiş ve karmaşıktır. |

Tablo 4.1: Toplu İş ve Akış Boru Hatları

Örneğin, akış boru hatları (streaming pipelines), TikTok gibi sosyal medya öneri sistemlerinde (social media recommender systems) son derece güçlüdür. Sosyal medyayı kullanırken, kullanıcı davranışı (user behavior) sık sık değişir. Tipik bir senaryo, belirli bir zamanda rahatlamak istemeniz ve çoğunlukla köpek yavruları videolarına bakmanızdır. Yine de 15 dakika sonra, eğitici içerik veya haberler gibi daha ciddi şeyler istiyorsunuz. Bu, öneri sisteminin (recommender system) bu davranış değişikliklerini gecikmeden yakalaması gerektiği anlamına gelir. İlgi alanları arasındaki geçiş döngüsel ve öngörülemez olduğundan, daha fazla içerik oluşturmak için her 30 dakikada bir veya her saat başı çalışan bir toplu iş boru hattı (batch pipeline) kullanamazsınız. Yeni içerik oluşturmak için her dakika çalıştırabilirsiniz, ancak aynı zamanda, çoğu tahminin tüketilmeyeceği için gereksiz maliyetlere yol açacaktır.

Akış boru hattı (streaming pipeline) uygulayarak, belirli kullanıcıların özelliklerini gerçek zamanlı olarak güncelleyebilirsiniz, bu özellikler daha sonra yeni önerileri tahmin eden bir model zincirine (chain of models) iletilir. Akış mimarileri (streaming architectures), Stripe veya PayPal'de kullanılanlar gibi gerçek zamanlı dolandırıcılık tespit algoritmalarının (real-time fraud detection algorithms) da omurgasıdır. Bu bağlamda, potansiyel olarak dolandırıcılık içeren işlemleri (potentially fraudulent transactions) bir toplu iş boru hattının (batch pipeline) işleyeceği gibi birkaç dakika veya saat sonra değil, meydana geldikçe tespit etmek kritik öneme sahiptir. Aynı aciliyet, piyasa verilerinin (market data) sürekli akışına dayalı hisse senedi tahminleri (stock predictions) yapan yüksek frekanslı ticaret platformları (high-frequency trading platforms) için de geçerlidir ve tüccarların milisaniye içinde karar almasını sağlar.

Diğer taraftan, çevrimdışı bir öneri sistemi (offline recommender system) için bir toplu iş mimarisi (batch architecture) kullanabilirsiniz. Örneğin, bir e-ticaret veya akış platformu için bir tane uygularken, sistemin çok reaktif olmasına gerek yoktur, çünkü kullanıcının davranışı nadiren değişir. Bu nedenle, toplu iş boru hattı (batch pipeline) kullanarak tarihsel kullanıcı davranışı verilerine (historical user behavior data) dayalı önerileri periyodik olarak, örneğin her gece güncellemek daha kolay ve daha ucuzdur.

Toplu iş boru hatlarının (batch pipelines) bir diğer popüler örneği, farklı kullanım durumları için veri çıkarmak (extract), dönüştürmek (transform) ve yüklemek (load) için kullanılan ETL (Extract, Transform, Load) tasarım modelidir. ETL tasarım modeli, verileri bir DB'den diğerine taşımak için kullanılan veri boru hatlarında (data pipelines) yaygındır. Bazı pratik kullanım durumları, birden fazla kaynaktan veri çıkarmak (extract), toplamak (aggregate) ve bir veri ambarına (data warehouse) bağlı bir gösterge tablosuna (dashboard) yüklemek gereken analizler için veri toplama işlemini içerir. Analiz alanları, e-ticaret ve pazarlamadan finans ve araştırmaya kadar çok çeşitli olabilir.

LLM Twin kullanım durumumuzda kullanılan veri toplama boru hattı (data collection pipeline), internetten veri çıkaran, yapılandıran ve gelecekteki işleme için bir veri ambarına yükleyen bir diğer ETL boru hattı örneğidir.

Tahmin veya özellik tazeliği (prediction or feature freshness) ile birlikte, toplu iş boru hatlarının akış boru hatlarına göre bir diğer dezavantajı, genellikle gereksiz tahminler yapmanızdır. Netflix gibi bir akış platformu için bir öneri sisteminin örneğini ele alalım. Her gece, tüm kullanıcılar için tahminler yaparsınız. Kullanıcıların büyük bir kısmının o gün giriş yapmayacak olması ihtimali yüksektir. Ayrıca, kullanıcılar genellikle tüm önerilere göz atmak yerine ilk önerilere yapışırlar. Bu nedenle, tahminlerin yalnızca bir kısmı kullanılır ve diğer tüm işlemler için hesaplama gücünü boşa harcarsınız.

Bu nedenle, popüler bir strateji, daha hızlı ve daha kolay uygulanabildiği için bir toplu iş mimarisiyle (batch architecture) başlamaktır. Ürün yerine konduktan sonra, maliyetleri azaltmak ve kullanıcı deneyimini iyileştirmek için kademeli olarak bir akış tasarımına (streaming design) geçersiniz.

Sonuç olarak, LLM Twin'in özellik boru hattını (feature pipeline) uygulamak için bir toplu iş mimarisi (batch architecture) (akış mimarisi yerine) aşağıdaki nedenlerle kullandık:

*   Anlık veri işleme gerektirmez (Does not require immediate data processing): Veri ambarını (data warehouse) ve özellik deposunu (feature store) senkronize etmek kritik olsa da, birkaç dakikalık bir gecikme kabul edilebilir. Bu nedenle, toplu iş boru hattını (batch pipeline) her dakika çalıştırmak üzere zamanlayabilir ve iki veri deposunu sürekli olarak senkronize edebiliriz. Bu teknik, veri hacminin (data volume) küçük olması nedeniyle işe yarar. Tüm veri ambarı yalnızca binlerce kayıt içerecektir, milyonlarca veya milyarlarca değil. Bu nedenle, bunları hızlı bir şekilde yineleyebilir ve iki DB'yi senkronize edebiliriz.
*   Basitlik (Simplicity): Daha önce de belirtildiği gibi, bir akış boru hattı (streaming pipeline) uygulamak iki kat daha karmaşıktır. Gerçek dünyada, sisteminizi mümkün olduğunca basit tutmak istersiniz, böylece anlaşılması, hata ayıklaması ve bakımı daha kolay olur. Ayrıca, basitlik genellikle daha düşük altyapı ve geliştirme maliyetleri anlamına gelir.

Şekil 8.10'da, mimarinize (akış ve toplu iş) ve işlemeniz gereken veri miktarına (küçük ve büyük veri) göre kullanabileceğiniz araçları karşılaştırıyoruz. Bizim kullanım durumumuzda, daha küçük veri ve toplu iş çeyreğindeyiz, burada vanilla Python ve LangChain, Sentence Transformers ve Unstructured gibi üretken AI araçlarının bir kombinasyonunu seçtik.

Şekil 4.10: Akış ve Toplu İş ve Daha Küçük ve Daha Büyük Veri Spektrumundaki Araçlar

Bu bölümde daha sonra, Değişim Veri Yakalama (Change Data Capture): Veri ambarını ve özellik deposunu senkronize etme bölümünde, bir toplu iş mimarisinden bir akış mimarisine geçmenin ne zaman mantıklı olduğu hakkında konuşacağız.

Kod açıklamaları:

Yukarıdaki metinde kod bulunmamaktadır. Ancak, bir sonraki bölümde kod örneği bulunması durumunda, kod satır satır açıklanacaktır.

Örneğin, aşağıdaki kod bloğu gibi bir örnek olabilir:
```python
# importing necessary libraries
import pandas as pd

# creating a sample dataframe
data = {'Name': ['John', 'Anna', 'Peter', 'Linda'],
        'Age': [28, 24, 35, 32],
        'Country': ['USA', 'UK', 'Australia', 'Germany']}
df = pd.DataFrame(data)

# displaying the dataframe
print(df)
```
Kod açıklaması:

1.  `import pandas as pd`: pandas kütüphanesini `pd` takma adıyla içe aktarır.
2.  `data = {...}`: örnek bir veri sözlüğü oluşturur.
3.  `df = pd.DataFrame(data)`: sözlükten bir pandas DataFrame oluşturur.
4.  `print(df)`: DataFrame'i yazdırır.

Bu kod, bir pandas DataFrame oluşturmak ve yazdırmak için kullanılır.

---

