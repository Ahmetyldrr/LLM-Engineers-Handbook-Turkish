# Adım Adım Eğiticiye Dalmak
#step-by-step-tutorial

Aşağıdaki ingilizce paragrafı bire bir türkçeye çevirerek yazıyorum.

# Eğiticiye Girişten (Tutorial Introduction) Önce Altyapıya Genel Bakış 
Before diving into the step-by-step tutorial, where we will show you how to set up all the necessary components, let’s briefly overview our infrastructure and how all the elements interact.

Adım adım eğiticiye dalmadan önce, gerekli tüm bileşenleri nasıl kuracağınızı göstereceğimiz yer, altyapımıza ve tüm öğelerin nasıl etkileşime girdiğine kısaca göz atalım.

This will help us in mindfully following the tutorials below.

Bu, aşağıdaki eğitimleri dikkatlice takip etmemize yardımcı olacaktır.

As shown in Figure 11.5 , we have a few services to set up.

Şekil 11.5'te gösterildiği gibi, kurmamız gereken birkaç servisimiz var.

To keep things simple, for MongoDB and Qdrant, we will leverage their serverless freemium version.

İşleri basit tutmak için, MongoDB ve Qdrant için sunucusuz (serverless) ücretsiz sürümlerini kullanacağız.

As for ZenML, we will leverage the free trial of the ZenML cloud, which will help us orchestrate all the pipelines in the cloud.

ZenML için ise ZenML bulutunun (cloud) ücretsiz deneme sürümünü kullanacağız, bu da bulut üzerindeki tüm işlem hatlarını (pipelines) düzenlemek için bize yardımcı olacaktır.

How will it do that? By leveraging the ZenML cloud, we can quickly allocate all the required AWS resources to run, scale, and store the ML pipeline.

Bunu nasıl yapacak? ZenML bulutunu kullanarak, ML işlem hattını çalıştırmak, ölçeklendirmek (scale) ve depolamak için gereken tüm AWS kaynaklarını (resources) hızlı bir şekilde tahsis edebiliriz.

It will help us spin up, with a few clicks, the following AWS components: 

Birkaç tıklama ile aşağıdaki AWS bileşenlerini (components) başlatmamıza yardımcı olacaktır:

An ECR service for storing Docker images 
Docker görüntülerini (images) depolamak için bir ECR servisi

An S3 object storage for storing all our artifacts and models 
Tüm eserlerimizi (artifacts) ve modellerimizi depolamak için bir S3 nesne depolama (object storage)

SageMaker Orchestrator for orchestrating, running, and scaling all our ML pipelines 
Tüm ML işlem hatlarımızı düzenlemek, çalıştırmak ve ölçeklendirmek için SageMaker Orchestrator

# Şekil 11.5: Altyapı Akışı (Infrastructure Flow)
Figure 11.5: Infrastructure flow

Now that we understand what the essential resources of our infrastructure are, let’s look over the core flow of running a pipeline in the cloud that we will learn to implement, presented in Figure 11.5 :

Şimdi altyapımızın temel kaynaklarının (resources) ne olduğunu anladığımıza göre, Şekil 11.5'te sunulan, bulut üzerinde bir işlem hattını çalıştırmaya dair temel akışı gözden geçirelim:

1. **Build a Docker image that contains all the system dependencies, the project dependencies, and the LLM Twin application.**
   - Tüm sistem bağımlılıklarını (dependencies), proje bağımlılıklarını ve LLM Twin uygulamasını içeren bir Docker görüntüsü (image) oluşturun.

2. **Push the Docker image to ECR , where SageMaker can access it.**
   - Docker görüntüsünü SageMaker'ın erişebileceği ECR'ye itin (push).

3. **Now, we can trigger any pipeline implemented during this book either from the CLI of our local machine or ZenML’s dashboard.**
   - Şimdi, bu kitap boyunca uyguladığımız herhangi bir işlem hattını, yerel makinemizin CLI'sından veya ZenML kontrol panelinden tetikleyebiliriz (trigger).

4. **Each step from ZenML’s pipeline will be mapped to a SageMaker job that runs on an AWS EC2 virtual machine ( VM ).**
   - ZenML işlem hattındaki her bir adım, bir AWS EC2 sanal makinesinde (VM) çalışan bir SageMaker görevi (job) ile eşlenecektir.

5. **Based on the dependencies between the directed acyclic graph ( DAG ) steps, some will run in parallel and others sequentially.**
   - Yönlendirilmiş döngüsüz çizge (DAG) adımları arasındaki bağımlılıklara göre, bazıları paralel olarak ve diğerleri sırayla çalışacaktır.

6. **When running a step, SageMaker pulls the Docker image from ECR, defined in step 2.**
   - Bir adımı çalıştırırken, SageMaker, 2. adımda tanımlanan ECR'den Docker görüntüsünü çeker (pull).

7. **Based on the pulled image, it creates a Docker container that executes the pipeline step.**
   - Çekilen görüntüye (image) dayanarak, işlem hattı adımını çalıştıracak bir Docker konteyneri (container) oluşturur.

8. **As the job is executed, it can access the S3 artifact storage, MongoDB, and Qdrant vector DB to query or push data.**
   - Görev çalıştırılırken, S3 eser depolama (artifact storage), MongoDB ve Qdrant vektör veritabanına (vector DB) erişerek veri sorgulayabilir veya veri gönderebilir.

The ZenML dashboard is a key tool, providing real-time updates on the pipeline’s progress and ensuring a clear view of the process.

ZenML kontrol paneli, işlem hattının ilerlemesi hakkında gerçek zamanlı güncellemeler sağlayan ve sürecin net bir görünümünü sağlayan kilit bir araçtır.

Now that we know how the infrastructure works, let’s start by setting up MongoDB, Qdrant, and the ZenML cloud.

Şimdi altyapının nasıl çalıştığını bildiğimize göre, MongoDB, Qdrant ve ZenML bulutunu kurarak başlayalım.

# Hangi AWS Bulut Bölgesi'ni (Cloud Region) Seçmeliyim?
What AWS cloud region should I choose?

Eğitimlerimizde, tüm servisler Frankfurt (eu-central-1) bölgesinde AWS'ye dağıtılacaktır.

In our tutorials, all the services will be deployed to AWS within the Frankfurt (eu-central-1) region.

Farklı bir bölge seçebilirsiniz, ancak bileşenler arasındaki daha hızlı yanıtları sağlamak ve olası hataları azaltmak için tüm servislerde tutarlı olun.

You can select another region, but be consistent across all the services to ensure faster responses between components and reduce potential errors.

# Servislerin Kullanıcı Arayüzlerindeki (UI) Değişiklikleri Nasıl Yönetmeliyim?
How should I manage changes in the services’ UIs?

Ne yazık ki, MongoDB, Qdrant veya diğer servisler kullanıcı arayüzlerini veya adlandırma kurallarını değiştirebilir.

Unfortunately, MongoDB, Qdrant, or other services may change their UI or naming conventions.

Bu kitabın her değiştiğinde güncellenmesi mümkün olmadığından, lütfen eğitimimizden farklı olan her şey için resmi belgelerine bakın.

As we can’t update this book each time that happens, please refer to their official documentation to check anything that differs from our tutorial.

Bu rahatsızlık için özür dileriz, ancak ne yazık ki bu bizim kontrolümüz dışında.

We apologize for this inconvenience, but unfortunately, it is not in our control.

Kodların ayrıntılı açıklamaları:

1. **Docker Görüntüsü Oluşturma (Building a Docker Image):**
   - `docker build -t my-docker-image .` komutu ile Dockerfile'da tanımlanan göre göre bir Docker görüntüsü oluşturulur.

2. **Docker Görüntüsünü ECR'ye İtme (Pushing Docker Image to ECR):**
   - `aws ecr get-login-password --region eu-central-1` ile ECR'ye giriş yapılır.
   - `docker tag my-docker-image:latest <account_id>.dkr.ecr.eu-central-1.amazonaws.com/my-docker-image:latest` ile Docker görüntüsüne ECR etiketi eklenir.
   - `docker push <account_id>.dkr.ecr.eu-central-1.amazonaws.com/my-docker-image:latest` ile Docker görüntüsü ECR'ye itinir.

3. **ZenML İşlem Hattını Tetikleme (Triggering ZenML Pipeline):**
   - `zenml pipeline run my_pipeline` komutu ile ZenML işlem hattı tetiklenir.

4. **SageMaker İşini Çalıştırma (Running SageMaker Job):**
   - SageMaker, ZenML tarafından tetiklenen her bir adımı bir SageMaker görevi olarak çalıştırır.

Bu adımların her biri, belirtilen altyapı bileşenleri kullanılarak gerçekleştirilir ve işlem hattının çalışması sağlanır.

---

