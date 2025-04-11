#Bölümün Son Kısımları: AWS Hesabı Kurulumu ve SageMaker'a Giriş

Bu bölümün son kısmı, bir AWS hesabı (eğer zaten yoksa), bir AWS erişim anahtarı (access key) ve CLI (Command Line Interface) kurulumuna odaklanacaktır. Ayrıca, SageMaker'ın ne olduğu ve neden kullanıldığını inceleyeceğiz. AWS'i bulut sağlayıcımız olarak seçtik çünkü en popüler olanıdır ve bizim (yazarlar) en fazla deneyime sahip olduğumuz buluttur. Gerçek şu ki, GCP (Google Cloud Platform) veya Azure gibi diğer büyük bulut sağlayıcıları da benzer hizmetler sunmaktadır. Bu nedenle, spesifik uygulamanıza bağlı olarak, geliştirme süresi (development time) (en fazla deneyime sahip olduğunuz), özellikler (features) ve maliyetler (costs) arasında her zaman bir denge vardır. Ancak MVP'miz (Minimum Viable Product) için AWS, ihtiyacımız olan her şey için sağlam özellikler (robust features) sunduğu için mükemmel bir seçenektir, örneğin S3 (object storage), ECR (container registry) ve SageMaker (eğitim ve çıkarım için hesaplama).

İngilizce Teknik Terimler Parantez İçinde:
Bu bölümün son kısmı, bir AWS hesabı (AWS account) (eğer zaten yoksa), bir AWS erişim anahtarı (AWS access key) ve CLI (Command Line Interface) kurulumuna odaklanacaktır. Ayrıca, SageMaker'ın ne olduğu ve neden kullanıldığını inceleyeceğiz. AWS'i bulut sağlayıcımız (cloud provider) olarak seçtik çünkü en popüler olanıdır ve bizim (yazarlar) en fazla deneyime sahip olduğumuz buluttur (cloud). Gerçek şu ki, GCP (Google Cloud Platform) veya Azure gibi diğer büyük bulut sağlayıcıları (cloud providers) da benzer hizmetler (similar services) sunmaktadır. Bu nedenle, spesifik uygulamanıza bağlı olarak, geliştirme süresi (development time) (en fazla deneyime sahip olduğunuz), özellikler (features) ve maliyetler (costs) arasında her zaman bir denge (trade-off) vardır. Ancak MVP'miz (Minimum Viable Product) için AWS, ihtiyacımız olan her şey için sağlam özellikler (robust features) sunduğu için mükemmel bir seçenektir (perfect option), örneğin S3 (Simple Storage Service - object storage), ECR (Elastic Container Registry - container registry) ve SageMaker (compute for training and inference - eğitim ve çıkarım için hesaplama).

Kodların Ayrıntılı Açıklaması:
Paragrafta kod bulunmamaktadır. Ancak aşağıdaki noktalar açıklanabilir:

- `AWS hesabı`: AWS (Amazon Web Services) kullanıcısı olmak için oluşturulan hesaptır.
- `AWS erişim anahtarı`: AWS hizmetlerine erişim sağlamak için kullanılan anahtardır.
- `CLI`: Command Line Interface, komut satırı arayüzü demektir. Kullanıcıların komutlar yazarak sistemle etkileşimde bulunmasını sağlar.
- `SageMaker`: AWS tarafından sunulan bir hizmettir, makine öğrenimi modellerinin geliştirilmesi, eğitilmesi ve dağıtılması için kullanılır.
- `S3`: Simple Storage Service, nesne depolama hizmetidir. Verileri depolamak ve yönetmek için kullanılır.
- `ECR`: Elastic Container Registry, konteyner kayıt hizmetidir. Docker konteynerlarını depolamak ve yönetmek için kullanılır.
- `GCP`: Google Cloud Platform, Google tarafından sunulan bir bulut bilişim platformudur.
- `Azure`: Microsoft tarafından sunulan bir bulut bilişim platformudur.
- `MVP`: Minimum Viable Product, bir ürünün en temel hali demektir. Bir ürünün temel özelliklerini içerir ve geliştirme sürecinde kullanılır.

---

