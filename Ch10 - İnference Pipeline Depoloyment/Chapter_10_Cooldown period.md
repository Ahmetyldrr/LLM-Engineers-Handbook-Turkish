# Ölçeklendirme Politikalarının (Scaling Policy) Önemli Bir Yönü: Bekleme Süresi (Cooldown Period)

Bir ölçeklendirme politikasının (scaling policy) bir diğer önemli yönü, tepki verme (responsiveness) ve kararlılık (stability) arasında denge kurulmasının gerektiği bekleme süresidir (cooldown period). Bu bekleme süresi, sisteminizin ölçeklendirme olayları sırasında aşırı tepki vermemesini sağlayan bir güvenlik tedbiri (safeguard) olarak işlev görür - ister kapasiteyi azaltma (scaling in) ister artırma (scaling out) olsun. Hesaplanmış bir duraklatma (pause) getirerek, bekleme süresi örnek sayısındaki hızlı dalgalanmaları önler. Özellikle, ölçek azaltma (scale-in) istekleri sırasında örneklerin kaldırılmasını geciktirir ve ölçek artırma (scale-out) istekleri sırasında yeni kopyaların (replica) oluşturulmasını kısıtlar. Bu strateji, LLM hizmeti için kararlı ve verimli bir ortamın korunmasına yardımcı olur.

Bu pratik temel bilgiler, çevrimiçi gerçek zamanlı makine öğrenimi (ML) sunucuları dahil olmak üzere çoğu web sunucusunun otomatik ölçeklendirilmesinde (autoscaling) kullanılır. SageMaker için ölçeklendirme politikalarını nasıl yapılandıracağınızı anladıktan sonra, öğrendiğiniz stratejileri Kubernetes veya AWS ECS gibi diğer popüler dağıtım araçlarına hemen uygulayabilirsiniz. Bu bölümde uygulanan AWS SageMaker uç noktası (endpoint) için otomatik ölçeklendirmeyi yapılandırma hakkında adım adım bir kılavuz için, AWS'nin resmi eğitimini takip edebilirsiniz: https://docs.aws.amazon.com/sagemaker/latest/dg/endpoint-auto-scaling-prerequisites.html .

Otomatik ölçeklendirme, herhangi bir bulut mimarisinde kritik bir bileşendir, ancak farkında olmanız gereken bazı tuzaklar vardır. İlk ve en tehlikeli olanı, altyapınızın maliyetlerini doğrudan etkileyen aşırı ölçeklendirmedir (over-scaling). Ölçeklendirme politikanız veya bekleme süreniz çok hassassa, gereksiz yere yeni makineleri çalıştırabilir ve bu makineler boşta kalabilir veya kaynaklar eksik kullanılabilir. İkinci neden, spektrumun diğer ucunda, sisteminizin yeterince ölçeklenmemesi ve son kullanıcı için kötü bir kullanıcı deneyimine neden olmasıdır. Bu nedenle iyi bir uygulama, sisteminizin gereksinimlerini anlamaktır. Bunlara dayanarak, otomatik ölçeklendirme parametrelerini bir geliştirme (dev) veya test ortamında, modelleri eğitirken hiperparametre ayarlama (hyperparameter tuning) yaparken olduğu gibi, tatlı nokta (sweet spot) bulana kadar ayarlamalı ve denemelısınız.

Örneğin, sisteminizin dakikada ortalama 100 kullanıcıyı desteklemesini ve bir tatil gibi bir aykırı değer (outlier) olayında dakikada 10.000 kullanıcıya kadar ölçeklenmesini beklediğinizi varsayalım. Bu spesifikasyonu kullanarak, sisteminizi stres testi (stress test) edebilir ve standart ve aykırı kullanım durumlarını destekleyen maliyetler, gecikme (latency) ve verim (throughput) arasındaki en iyi dengeyi bulmak için kaynaklarınızı izleyebilirsiniz.

**Kod Açıklaması**

Bu metinde kod bulunmamaktadır. Ancak, eğer bir kod örneği verilseydi, her bir satırın altında ayrıntılı olarak açıklanacaktı.

**İngilizce Teknik Terimlerin Türkçe Karşılıkları ve Parantez İçinde İngilizce Teknik Terimlerin Yazılması**

- scaling policy: ölçeklendirme politikası
- cooldown period: bekleme süresi
- responsiveness: tepki verme
- stability: kararlılık
- scaling in: kapasiteyi azaltma
- scaling out: kapasiteyi artırma
- replica: kopya
- autoscaling: otomatik ölçeklendirme
- hyperparameter tuning: hiperparametre ayarlama
- dev: geliştirme ortamı
- outlier: aykırı değer
- stress test: stres testi
- latency: gecikme
- throughput: verim

Yukarıdaki metinde, İngilizce teknik terimlerin Türkçe karşılıkları verildi ve orijinal metinde olduğu gibi bırakıldı. Ayrıca, parantez içinde İngilizce teknik terimler eklendi.

---

