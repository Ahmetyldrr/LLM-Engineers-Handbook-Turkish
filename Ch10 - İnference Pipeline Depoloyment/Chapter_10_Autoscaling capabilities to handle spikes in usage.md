# SageMaker LLM Mikroservisi için Otomatik Ölçeklendirme (Autoscaling)

Şimdiye kadar, SageMaker LLM mikroservisi, kullanıcılarımıza hizmet vermek için statik bir replika sayısı kullandı; bu, tüm zaman boyunca, trafikten bağımsız olarak, aynı sayıda örnek (instance) çalıştırdığı anlamına geliyor. Bu kitapta vurguladığımız gibi, GPU'lu makineler pahalıdır. Bu nedenle, çoğaltmaların çoğunun boşta olduğu kapama süresinde çok fazla para kaybediyoruz. Ayrıca, uygulamamız ani trafik artışları yaşarsa, sunucu istek sayısını karşılayamadığı için uygulama kötü performans gösterecektir. Bu, uygulamamızın kullanıcı deneyimi için büyük bir sorundur, çünkü bu artışlar sırasında yeni kullanıcıların çoğunu kazanıyoruz. Dolayısıyla, ürünümüzden kötü bir izlenim edinirlerse, platformumuza geri dönme şanslarını önemli ölçüde azaltırız.

Önceden, SageMaker'dan ResourceRequirements sınıfını kullanarak çoklu uç nokta (multi-endpoint) servisimizi yapılandırdık. Örneğin, aşağıdaki hesaplama gereksinimleriyle dört kopya (replika) istediğimizi varsayalım:
```python
model_resource_config = ResourceRequirements(
    requests={
        "copies": 4,  # Replika sayısı.
        "num_accelerators": 4,  # Gerekli GPU sayısı.
        "num_cpus": 8,  # Gerekli CPU çekirdek sayısı.
        "memory": 5 * 1024,  # Mb cinsinden minimum gerekli bellek (gerekli)
    },
)
```
Bu yapılandırmayı kullanarak, boş zaman veya trafik artışlarından bağımsız olarak her zaman dört replika istemciye hizmet verir. Çözüm, replika sayısını çeşitli ölçümlere (metric) göre dinamik olarak ölçeklendiren bir otomatik ölçeklendirme (autoscaling) stratejisi uygulamaktır; örneğin, istek sayısı gibi.

Örneğin, Şekil 10.8, SageMaker Inference uç noktalarının (endpoint) istek sayısına göre ölçeklendiği standart bir mimariyi göstermektedir. Trafik yokken, sunucunun yeni kullanıcı isteklerine yanıt vermeye devam etmesi için bir replika çevrimiçi olabilir veya gecikme kritik değilse sıfıra ölçeklenebilir. Ardından, saniyede yaklaşık 10 istek olduğunda, iki replika çevrimiçi tutmamız gerektiğini ve istek sayısının saniyede 100'e yükseldiğinde, otomatik ölçeklendirme servisinin talebi karşılamak için 20 replika kadar artırması gerektiğini varsayalım. Bu sayıların, sizin özel kullanım durumunuza uyarlanması gereken kurgusal sayılar olduğuna dikkat edin.

# Otomatik Ölçeklendirme Mimarisi

Bulut ağının (cloud networking) küçük ayrıntılarına girmeksizin, çoklu replika sistemleriyle çalışırken, istemci ve replika arasında bir Uygulama Yük Dengeleyici (Application Load Balancer, ALB) veya başka bir tür yük dengeleyici bulunur. Tüm istekler önce ALB'ye gider ve ALB'nin bunları bir replika'ya yönlendirmesini bilir. ALB, çeşitli yönlendirme stratejileri benimseyebilir; en basit olanı, sırayla her replika'ya bir istek gönderen round robin olarak adlandırılır. Örneğin, ilk istek replika bir'e, ikinci istek replika iki'ye yönlendirilir ve bu şekilde devam eder.

Bu yaklaşımı kullanarak, çevrimiçi olarak kaç replika bulunduğunuzdan bağımsız olarak, istemcinin çağırdığı uç nokta her zaman kümenizin giriş noktası olarak görev yapan yük dengeleyici tarafından temsil edilir. Böylece, yeni replika eklemek veya kaldırmak sunucu ve istemci iletişim protokolünü etkilemez.

# SageMaker Inference Uç Noktası için Otomatik Ölçeklendirme Stratejisi Uygulama

SageMaker, önceden tanımlanmış politikalara göre kaynakları dinamik olarak ölçeklendirmenize olanak tanıyan Uygulama Otomatik Ölçeklendirme (Application Auto Scaling) adlı bir özellik sağlar. Bu işlevselliği etkili bir şekilde kullanmak için iki temel adım vardır: ölçeklenebilir bir hedef (scalable target) kaydetmek ve ölçeklenebilir bir politika (scalable policy) oluşturmak.

Kod Açıklaması:

1. `model_resource_config = ResourceRequirements(requests={...})`: Bu satır, SageMaker'da bir model için kaynak gereksinimlerini tanımlayan bir yapılandırma nesnesi oluşturur.
   - `"copies": 4`: 4 replika talep eder.
   - `"num_accelerators": 4`: 4 GPU talep eder.
   - `"num_cpus": 8`: 8 CPU çekirdeği talep eder.
   - `"memory": 5 * 1024`: 5 GB bellek talep eder.

2. Otomatik ölçeklendirme stratejisi, replika sayısını dinamik olarak ayarlamak için kullanılır. Bu, özellikle talepteki ani artışlar veya azalışlar için kullanışlıdır.

3. `Application Load Balancer (ALB)`: İstekleri replika'lara dağıtan bir yük dengeleyici türüdür. Bu, sistemde yüksek kullanılabilirlik ve ölçeklenebilirlik sağlar.

4. SageMaker'ın `Application Auto Scaling` özelliği, kaynakları dinamik olarak ölçeklendirmek için kullanılır. Bu, iki temel adım içerir: ölçeklenebilir bir hedef kaydetmek ve bir ölçeklendirme politikası oluşturmak.

---

