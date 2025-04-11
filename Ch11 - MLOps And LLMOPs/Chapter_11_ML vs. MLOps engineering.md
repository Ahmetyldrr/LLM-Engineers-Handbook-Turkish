#ML Mühendisliği ve MLOps Arasındaki İnce Çizgi
There is a fine line between ML engineering and MLOps. If we want to define a rigid job description for the two rules, it cannot be easy to completely differentiate what responsibilities go into ML engineering ( MLE ) and what goes into MLOps.
ML mühendisliği (ML Engineering) ve MLOps arasında ince bir çizgi vardır. Eğer bu iki rol için katı bir iş tanımı yapmak istiyorsak, ML mühendisliğine (MLE) hangi sorumlulukların gireceğini ve MLOps'a neyin gireceğini tamamen ayırt etmek kolay olamaz.

I have seen many job roles that bucket the MLOps role with the platform and cloud engineers. 
MLOps rolünü platform ve bulut mühendisleri ile birleştiren birçok iş rolü gördüm.

From one perspective, that makes a lot of sense: as an MLOps engineer, you have a lot of work to do on the infrastructure side. 
Bir açıdan bakıldığında, bu çok mantıklı: MLOps mühendisi olarak, altyapı tarafında yapacak çok işiniz var.

On the other hand, as seen in this section, an MLOps engineer still has to implement things such as experiment tracking (experiment tracking), model registries (model registries), versioning (versioning), and more. 
Diğer taraftan, bu bölümde görüldüğü gibi, bir MLOps mühendisi hala deney takibi (experiment tracking), model kayıtları (model registries), sürümleme (versioning) gibi şeyleri uygulamak zorundadır.

A good strategy would be to let the ML engineer integrate these into the code and the MLOps engineer focus on making them work on their infrastructure. 
İyi bir strateji, ML mühendisine bunları koda entegre etmesine izin vermek ve MLOps mühendisine altyapılarında çalışmasını sağlamak olacaktır.

At a big corporation, ultimately, differentiating the two roles might make sense. 
Büyük bir şirket içinde, nihayetinde, iki rolü birbirinden ayırmak anlamlı olabilir.

But when working in small to medium-sized teams, you will wear multiple hats and probably work on the ML system’s MLE and MLOps aspects. 
Ama küçük ve orta büyüklükteki takımlarda çalışırken, birden fazla şapka takacaksınız ve muhtemelen ML sisteminin hem MLE hem de MLOps yönleri üzerinde çalışacaksınız.

#DS vs. MLE vs. MLOps
Figure 11.3: DS vs. MLE vs. MLOps 
Şekil 11.3: DS vs. MLE vs. MLOps

For instance, in Figure 11.3 , we see a clear division of responsibilities among the three key roles: data scientist/ML researcher, ML engineer, and MLOps engineer. 
Örneğin, Şekil 11.3'te, üç ana rol arasındaki sorumlulukların açık bir şekilde bölündüğünü görüyoruz: veri bilimcisi/ML araştırmacısı, ML mühendisi ve MLOps mühendisi.

The Data Scientist ( DS ) implements specific models to address problems. 
Veri Bilimcisi (DS) sorunları çözmek için özel modeller uygular.

The ML engineer takes the functional models from the DS team and constructs a layer on top of them, making them modular and extendable and providing access to a database ( DB ) or exposing them as an API over the internet. 
ML mühendisi, DS takımından işlevsel modelleri alır ve onların üzerine bir katman oluşturur, onları modüler ve genişletilebilir hale getirir ve bir veritabanına (DB) erişim sağlar veya onları internet üzerinden bir API olarak kullanıma sunar.

However, the MLOps engineer plays a pivotal role in this process. 
Ancak, MLOps mühendisi bu süreçte kilit bir rol oynar.

They take the code from this intermediate layer and place it on a more generic layer, the infrastructure. 
Bu ara katmandan kodu alır ve daha genel bir katmana, altyapıya yerleştirir.

This action marks the application’s transition to production. 
Bu eylem, uygulamanın üretime geçişini işaretler.

From this point, we can start thinking about automation (automation), monitoring (monitoring), versioning (versioning), and more. 
Bu noktadan itibaren, otomasyon (automation), izleme (monitoring), sürümleme (versioning) ve daha fazlasını düşünmeye başlayabiliriz.

The intermediate layer differentiates a proof of concept from an actual product. 
Ara katman, bir kanıtı gerçek bir üründen ayırır.

In that layer, you design an extendable application that has a state by integrating a DB and is accessible over the internet through an API. 
Bu katmanda, bir DB entegre ederek durumunu koruyan ve internet üzerinden bir API aracılığıyla erişilebilen genişletilebilir bir uygulama tasarlarsınız.

When shipping the application on a specific infrastructure, you must consider scalability (scalability), latency (latency), and cost-effectiveness (cost-effectiveness). 
Uygulamayı belirli bir altyapıda gönderirken, ölçeklenebilirlik (scalability), gecikme (latency) ve maliyet etkinliğini (cost-effectiveness) göz önünde bulundurmanız gerekir.

Of course, the intermediate and generic layers depend on each other, and often, you must reiterate to meet the application requirements. 
Tabii ki, ara ve genel katmanlar birbirine bağlıdır ve genellikle uygulama gereksinimlerini karşılamak için yinelemeye gitmeniz gerekir.

**Kod Açıklaması**
Yukarıdaki metinde kod bulunmamaktadır. Ancak, aşağıdaki gibi bir kod örneği verilebilir:
```python
# Veri bilimcisi tarafından uygulanan model
def model(x):
    return x * 2

# ML mühendisi tarafından uygulanan ara katman
class IntermediateLayer:
    def __init__(self, model):
        self.model = model

    def predict(self, x):
        return self.model(x)

# MLOps mühendisi tarafından uygulanan altyapı
class Infrastructure:
    def __init__(self, intermediate_layer):
        self.intermediate_layer = intermediate_layer

    def deploy(self):
        # Uygulamayı üretime gönder
        pass

# Kullanım örneği
model = model(x=2)
intermediate_layer = IntermediateLayer(model)
infrastructure = Infrastructure(intermediate_layer)
infrastructure.deploy()
```
Bu kod örneğinde, veri bilimcisi tarafından uygulanan model, ML mühendisi tarafından uygulanan ara katman ve MLOps mühendisi tarafından uygulanan altyapı gösterilmektedir.

---

