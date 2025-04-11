# Mikroservis Mimarisi
Bir mikroservis mimarisi, çıkarım hattını (inference pipeline) ayrı, bağımsız hizmetlere böler—tipik olarak LLM hizmetini ve iş mantığını (business logic) farklı bileşenlere ayırır. Bu hizmetler, REST veya gRPC gibi protokolleri kullanarak bir ağ üzerinden iletişim kurar. Şekil 10.3'te gösterildiği gibi, bu yaklaşımın ana avantajı, her bileşeni bağımsız olarak ölçeklendirme (scale) yeteneğidir. Örneğin, LLM hizmeti iş mantığından daha fazla GPU kaynağı gerektirebileceğinden, diğer bileşenleri etkilemeden yatay olarak ölçeklendirilebilir (horizontally scale). Bu, kaynak kullanımını optimize eder ve maliyetleri azaltır, çünkü farklı türdeki makineler (örneğin, GPU ve CPU) her hizmetin ihtiyaçlarına göre kullanılabilir. Örneğin, LLM çıkarımı daha uzun sürerse, talebi karşılamak için daha fazla ML hizmeti kopyası (replica) gerekir. Ancak GPU sanal makinelerinin (VM) pahalı olduğunu unutmayın. İki bileşeni ayırarak, yalnızca GPU makinesinde gerekli olanı çalıştıracak ve diğer hesaplamaları çok daha ucuz bir makinede yaparak GPU sanal makinesini bloke etmeyeceksiniz. Böylece, bileşenleri ayırarak, maliyetleri minimize ederek yatay olarak ölçeklendirebilir ve sisteminizin ihtiyaçlarına uygun maliyet etkin bir çözüm sağlayabilirsiniz.

Şekil 10.3: Hesaplama gereksinimlerine göre mikroservisleri bağımsız olarak ölçeklendirme

Ayrıca, her mikroservis en uygun teknoloji yığınına (technology stack) sahip olabilir, bu da takımların bağımsız olarak yenilik yapmasına ve optimize etmesine olanak tanır. Ancak, mikroservisler dağıtım ve bakımda karmaşıklık (complexity) getirir. Her hizmet ayrı olarak dağıtılmalı, izlenmeli ve bakım yapılmalıdır, bu da monolitik bir sistem yönetmekten daha zor olabilir. Hizmetler arasındaki artan ağ iletişimi (network communication) de gecikme (latency) ve potansiyel başarısızlık noktaları (points of failure) getirebilir, bu da sağlam izleme ve direnç mekanizmaları (resilience mechanisms) gerektirir.

ML modelini ve iş mantığını iki hizmete ayırma önerisinin gerektiğinde genişletilebileceğini unutmayın. Örneğin, veri ön işleme (preprocessing) için bir hizmet, model için bir hizmet ve veri son işleme (post-processing) için başka bir hizmet olabilir. Dört temel direğe (latency, throughput, data, ve infrastructure) bağlı olarak, uygulama ihtiyaçlarınıza en uygun mimariyi yaratıcı bir şekilde tasarlayabilirsiniz.

**Kod Açıklaması:**

Paragrafın altında kod bulunmamaktadır, ancak metinde geçen bazı teknik terimlerin açıklamaları aşağıda verilmiştir:

- `Mikroservis Mimarisi`: Büyük bir uygulamayı küçük, bağımsız hizmetlere bölme yaklaşımı.
- `Çıkarım Hattı (Inference Pipeline)`: Makine öğrenimi modelinin girdi verilerini işleyerek çıktı üretmesini sağlayan süreç.
- `LLM Hizmeti`: Büyük Dil Modeli (Large Language Model) hizmeti.
- `İş Mantığı (Business Logic)`: Uygulamanın iş kurallarını ve mantığını içeren kod.
- `REST` ve `gRPC`: Hizmetler arasında iletişim kurmak için kullanılan protokoller.
- `Ölçeklendirme (Scale)`: Uygulamanın artan talepleri karşılamak için kaynaklarını artırma yeteneği.
- `Yatay Ölçeklendirme (Horizontally Scale)`: Uygulamayı daha fazla makine ekleyerek ölçeklendirme.
- `GPU` ve `CPU`: Farklı hesaplama görevleri için kullanılan işlemci türleri.
- `Sanal Makine (VM)`: Sanal bir ortamda çalışan işletim sistemi örneği.
- `Replika (Replica)`: Bir hizmetin veya veritabanının kopyası.
- `Teknoloji Yığını (Technology Stack)`: Uygulama geliştirmek için kullanılan teknoloji ve araçlar seti.
- `Ağ İletişimi (Network Communication)`: Hizmetler arasında veri alışverişi.
- `Gecikme (Latency)`: İstek ile yanıt arasındaki zaman farkı.
- `Başarısızlık Noktaları (Points of Failure)`: Sistemde hata veya başarısızlık oluşabilecek noktalar.
- `Direnç Mekanizmaları (Resilience Mechanisms)`: Sistemin hata veya başarısızlıklara karşı direncini artıran mekanizmalar.

---

