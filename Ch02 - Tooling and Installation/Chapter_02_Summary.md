#Bu Bölümde Neler Öğrendik
Bu bölümde, kitap boyunca kullanılan temel araçları inceledik. İlk olarak, deposunu destekleyen doğru Python sürümünü nasıl kuracağımızı anladık. Ardından, sanal bir ortam oluşturmayı ve Poetry kullanarak tüm bağımlılıkları (dependencies) yüklemeyi gözden geçirdik. Son olarak, uygulamayı çalıştırmak için gereken tüm komutları toplamak amacıyla Poe the Poet gibi bir görev yürütme aracını (task execution tool) nasıl kullanacağımızı anladık. Bir sonraki adım, model paylaşımı için bir model kaydı (model registry), eğitim deneylerimizi yönetmek için bir deney izleyici (experiment tracker), tüm makine öğrenimi ardışık düzenlerimizi (ML pipelines) ve eserlerimizi (artifacts) yönetmek için bir orkestrator ve tüm dosyalarımızı ve veri kümelerimizi (datasets) yönetmek için meta verileri (metadata) içeren MLOps en iyi uygulamalarını (best practices) sağlamak için kullanılan tüm araçları gözden geçirmekti. Ayrıca, LLM Twin kullanım senaryosunu uygulamak için ne tür veritabanlarına (databases) ihtiyacımız olduğunu anladık. Son olarak, bir AWS hesabı oluşturma, erişim anahtarı (access key) oluşturma ve AWS CLI'yi programatik erişim için yapılandırma sürecini inceledik. Ayrıca, AWS SageMaker'ı ve LLM Twin uygulamamızı oluşturmak için onu neden seçtiğimizi derinlemesine anladık. Bir sonraki bölümde, internetten gönderileri, makaleleri ve depoları kazıyan (scrapes) ve bunları bir veri ambarında (data warehouse) depolayan veri toplama ETL'si (data collection ETL) ile başlayarak LLM Twin projesinin uygulanmasını inceleyeceğiz.

#İngilizce Teknik Terimlerin Türkçeleri ve Açıklamaları
- **dependencies**: Bağımlılıklar, bir projenin çalışması için gerekli olan kütüphaneler veya paketlerdir.
- **task execution tool**: Görev yürütme aracı, belirli görevleri otomatize etmek için kullanılan bir araçtır. (Poe the Poet gibi)
- **model registry**: Model kaydı, makine öğrenimi modellerini depolamak, yönetmek ve paylaşmak için kullanılan bir sistemdir.
- **experiment tracker**: Deney izleyici, makine öğrenimi deneylerini izlemek, yönetmek ve karşılaştırmak için kullanılan bir araçtır.
- **orchestrator**: Orkestrator, birden fazla görevi veya işlemi koordine etmek ve yönetmek için kullanılan bir sistem veya araçtır.
- **ML pipelines**: Makine öğrenimi ardışık düzenleri, ham verileri işlenmiş verilere dönüştürmek ve makine öğrenimi modellerini eğitmek için kullanılan bir dizi adımdır.
- **artifacts**: Eserler, bir makine öğrenimi projesinde kullanılan veya üretilen çeşitli dosyalar, modeller veya diğer çıktılar olabilir.
- **metadata**: Meta veriler, verilerin yapısını, içeriğini veya bağlamını açıklayan verilerdir.
- **databases**: Veritabanları, verileri depolamak, yönetmek ve sorgulamak için kullanılan sistemlerdir.
- **ETL**: Extract, Transform, Load (Çıkarma, Dönüştürme, Yükleme) anlamına gelir. Verileri çeşitli kaynaklardan toplamak, işlemek ve bir hedef sistemde depolamak için kullanılan bir süreçtir.
- **data warehouse**: Veri ambarı, çeşitli kaynaklardan toplanan verileri depolamak ve analiz etmek için kullanılan bir sistemdir.

#Kodların Ayrıntılı Açıklaması
Bu metinde doğrudan kod bulunmamaktadır. Ancak, anlatılan bazı konularla ilgili örnek kodlar aşağıdaki gibi olabilir:

1. **Poetry ile Bağımlılıkları Yüklemek**
   ```bash
   # Poetry ile bağımlılıkları yüklemek için
   poetry install
   ```
   Bu komut, `pyproject.toml` dosyasındaki tanımlı bağımlılıkları yükler.

2. **Poe the Poet ile Görevleri Yürütmek**
   ```bash
   # Poe the Poet ile bir görev yürütmek için
   poe task_name
   ```
   Burada `task_name`, `pyproject.toml` içinde tanımlanmış bir görev adını temsil eder.

3. **AWS CLI'yi Yapılandırmak**
   ```bash
   # AWS CLI'yi yapılandırmak için
   aws configure
   ```
   Bu komut, AWS CLI'yi yapılandırmak için kullanılır. Erişim anahtarınızı ve diğer ayarları girmenizi ister.

4. **AWS SageMaker ile Çalışmak**
   AWS SageMaker ile çalışmak için genellikle AWS Management Console'u veya AWS SDK'larını kullanırsınız. Örneğin, Python'da Boto3 kütüphanesini kullanarak SageMaker ile etkileşime geçebilirsiniz:
   ```python
   import boto3

   sagemaker = boto3.client('sagemaker')

   # SageMaker'da bir iş tanımlamak için
   response = sagemaker.create_training_job(
       TrainingJobName='example-training-job',
       # Diğer gerekli parametreler...
   )
   print(response)
   ```
   Bu örnek, SageMaker'da bir eğitim işi oluşturmak için Boto3'ü kullanır.

---

