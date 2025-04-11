# DevOps ve MLOps: Uygulama Yaşam Döngüsü Uygulamaları

DevOps, uygulama yaşam döngüsü boyunca çeşitli uygulamaları kapsar, ancak bu kitap boyunca değineceğimiz temel olanlar şunlardır:
 
Deployment ortamları (Deployment environments): Kodunuzu üretime göndermeden önce iyice test etmek için, üretim ortamını taklit eden birden fazla üretim öncesi ortam tanımlamanız gerekir. En yaygın yaklaşım, geliştiricilerin son özelliklerini test edebilecekleri bir geliştirme (dev) ortamı oluşturmaktır. Ardından, QA ekibi ve paydaşların (stakeholders), kullanıcılara göndermeden önce hataları bulmak ve son özellikleri deneyimlemek için uygulama ile oynayabilecekleri bir hazırlık (staging) ortamı vardır. Son olarak, son kullanıcılara açık olan üretim ortamımız (production environment) vardır.

Versiyon kontrolü (Version control): Kaynak kodda yapılan her değişikliği izlemek, yönetmek ve versiyonlamak için kullanılır. Bu, kodun ve deployment süreçlerinin evrimi üzerinde tam kontrol sahibi olmanızı sağlar. Örneğin, versiyonlama olmadan, geliştirme, hazırlık ve üretim ortamları arasındaki değişiklikleri izlemek imkansız olurdu. Yazılımınızı versiyonlayarak, hangi versiyonun kararlı ve gönderilmeye hazır olduğunu her zaman bilirsiniz.

Sürekli entegrasyon (Continuous Integration - CI): Kodu geliştirme, hazırlık ve üretim ana dallarına (main branches) göndermeden önce, uygulamanızı otomatik olarak derler (build) ve her değişiklikte otomatik testler çalıştırırsınız. Otomatik testlerin tümü geçtikten sonra, özellik dalı (feature branch) ana dal ile birleştirilebilir (merged).

Sürekli teslimat (Continuous Delivery - CD): Sürekli teslimat, CI ile birlikte çalışır ve altyapı sağlama (infrastructure provisioning) ve uygulama deployment adımlarını otomatikleştirir. Örneğin, kod hazırlık ortamına birleştirildikten sonra, son değişikliklere sahip uygulama otomatik olarak hazırlık altyapınız üzerinde deploy edilir. Ardından, QA ekibi (veya paydaşlar), son özelliklerin beklendiği gibi çalışıp çalışmadığını doğrulamak için manuel test etmeye başlar. Bu iki adım genellikle birlikte CI/CD olarak adlandırılır.

DevOps'un bir dizi temel prensip önerdiğini, ancak bunların platform/araç agnostik olduğunu unutmayın. Ancak, LLM Twin kullanım durumumuzda, kodun evrimini izlemek için GitHub kullanarak bir versiyon kontrol katmanı ekleyeceğiz. Versiyon kontrol için bir başka popüler araç GitLab'dir. CI/CD pipeline'unu uygulamak için, GitHub ekosistemini ve açık kaynak projeler için ücretsiz olan GitHub Actions'ı kullanacağız. Diğer araç seçenekleri arasında GitLab CI/CD, CircleCI ve Jenkins bulunur. Genellikle, geliştirme ortamınız, özelleştirme ve gizlilik ihtiyaçlarınıza göre bir DevOps aracı seçersiniz. Örneğin, Jenkins açık kaynak bir DevOps aracıdır ve kendiniz barındırabilir ve tam olarak kontrol edebilirsiniz. Dezavantajı ise, bir karmaşıklık katmanı ekleyerek kendiniz barındırmanız ve bakımını yapmanız gerektiğidir. Bu nedenle, birçok şirket GitHub Actions veya GitLab CI/CD gibi versiyon kontrol ekosistemleriyle en iyi çalışanı seçer.

Şimdi DevOps'u sağlam bir şekilde anladığımıza göre, MLOps alanının AI/ML dünyasında aynı temel ilkeleri korumak için nasıl ortaya çıktığını keşfedelim.

### Kod Açıklaması

Yukarıdaki metinde kod bulunmamaktadır. Ancak, metinde adı geçen bazı teknik terimlerin ve araçların açıklaması aşağıda verilmiştir:

- **Deployment environments**: Uygulamanın farklı aşamalarda (geliştirme, hazırlık, üretim) deploy edildiği ortamlar.
- **Version control**: Kod değişikliklerini izlemek ve yönetmek için kullanılan sistemler (örneğin GitHub, GitLab).
- **Continuous Integration (CI)**: Kod değişikliklerini otomatik olarak derlemek ve test etmek.
- **Continuous Delivery (CD)**: Uygulama deployment sürecini otomatikleştirmek.
- **CI/CD pipeline**: CI ve CD süreçlerini birleştiren otomatikleştirilmiş süreç.
- **GitHub Actions**: GitHub'da CI/CD pipeline'larını uygulamak için kullanılan bir araç.
- **GitLab CI/CD**: GitLab'da CI/CD pipeline'larını uygulamak için kullanılan bir araç.
- **Jenkins**: Açık kaynak bir CI/CD aracı.

Bu teknik terimlerin ve araçların açıklamaları, metinde bahsi geçen DevOps ve MLOps kavramlarını daha iyi anlamak için yararlı olabilir.

---

