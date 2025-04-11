# LLM Twin'in Çıkarım İşlem Hattının Uygulanması

Artık LLM Twin'in çıkarım işlem hattının dağıtım stratejisini uygulamak için mevcut tüm tasarım seçimlerini anladığımıza göre, bunu gerçekleştirmek için somut kararları inceleyelim. Birincil amacımız içerik oluşturmayı kolaylaştıran bir sohbet robotu geliştirmektir. Bunu başarmak için, düşük gecikme süresine (low latency) güçlü bir vurgu yaparak istekleri sırayla işleyeceğiz. Bu, çevrimiçi gerçek zamanlı çıkarım dağıtım mimarisinin (online real-time inference deployment architecture) seçilmesini gerektirir. Monolit ve mikro hizmet yönüyle ilgili olarak, makine öğrenimi (ML) hizmetini, iş mantığını içeren bir REST API sunucusu ile verilen LLM'yi çalıştırmak için optimize edilmiş bir LLM mikro hizmeti arasında böleceğiz. LLM'nin çıkarımı çalıştırmak için güçlü bir makine gerektirmesi ve gecikme süresini ve bellek kullanımını hızlandırmak için çeşitli motorlarla daha da optimize edilebilmesi nedeniyle, mikro hizmet mimarisini seçmek en mantıklı seçimdir. Bunu yaparak, çeşitli LLM boyutlarına göre altyapıyı hızla uyarlayabiliriz. Örneğin, 8B parametre modelini çalıştırırsak, model, nicemleme (quantization) sonrasında Nivida A10G GPU'lu tek bir makinede çalışabilir. Ancak 30B modelini çalıştırmak istersek, Nvidia A100 GPU'ya yükseltebiliriz. Bunu yapmak, REST API'yi değiştirmeden yalnızca LLM mikro hizmetini yükseltmemizi sağlar.

Şekil 10.4'te gösterildiği gibi, iş mantığının çoğu bizim özel kullanım durumumuzda RAG (Retrieval-Augmentation-Generation) etrafında toplanmıştır. Bu nedenle, RAG'nin alma ve artırma (retrieval and augmentation) bölümlerini iş mikro hizmeti içinde gerçekleştireceğiz. Ayrıca, önceki bölümde sunulan gelişmiş RAG tekniklerinin tümünü içerecek ve ön alma, alma ve alma sonrası adımlarını optimize edecektir. LLM mikro hizmeti kesinlikle RAG oluşturma bileşeni için optimize edilmiştir. Sonuç olarak, iş katmanı, kullanıcı sorgusu, istem, cevap ve diğer ara adımlardan oluşan istem izini (prompt trace), 11. Bölüm'de ayrıntılandıracağımız istem izleme işlem hattına (prompt monitoring pipeline) gönderecektir.

Özetle, yaklaşımımız, LLM ve iş mantığını iki ayrı hizmete ayıran bir mikro hizmet mimarisi kullanarak çevrimiçi gerçek zamanlı bir makine öğrenimi hizmeti uygulamayı içerir.

# Çıkarım İşlem Hattının Arayüzü

Çıkarım işlem hattının arayüzünü, Özellik/Eğitim/Çıkarım (Feature/Training/Inference - FTI) mimarisi tarafından tanımlandığı şekilde inceleyelim. İşlem hattının çalışması için iki şeye ihtiyacı vardır:
- RAG için kullanılan gerçek zamanlı özellikler (real-time features), özellik işlem hattı tarafından oluşturulur ve çevrimiçi özellik deposundan (online feature store), daha somut olarak Qdrant vektör veritabanından (DB) sorgulanır.
- Eğitim işlem hattı tarafından oluşturulan ve model kayıt defterinden (model registry) çekilen ince ayarlı bir LLM.

Bunu akılda tutarak, makine öğrenimi hizmetinin akışı Şekil 10.4'te gösterildiği gibi görünür:
1. Bir kullanıcı bir HTTP isteği aracılığıyla bir sorgu gönderir.
2. Kullanıcının girdisi, 4. Bölüm'de uygulanan gelişmiş RAG alma modülü kullanılarak uygun bağlamı alır.
3. Kullanıcının girdisi ve alınan bağlam, özel bir istem şablonu kullanılarak son isteme paketlenir.
4. İstem, bir HTTP isteği aracılığıyla LLM mikro hizmetine gönderilir.
5. İş mikro hizmetleri oluşturulan cevabı bekler.
6. Cevap oluşturulduktan sonra, izleme için kullanıcının girdisi ve diğer hayati bilgilerle birlikte istem izleme işlem hattına gönderilir.
7. Sonuç olarak, oluşturulan cevap kullanıcıya geri gönderilir.

# Kullanılan Teknoloji Yığını

Şekil 10.4'te sunulan mimariyi uygulamak için kullandığımız teknoloji yığınına göz atalım. Vektör veritabanı için Qdrant'ı kullandığımızı biliyoruz. Model kayıt defteri için Hugging Face'i kullanacağız. Bunu yaparak, modelimizi bu kitaptan kodu test eden herkesle paylaşabiliriz. Böylece, eğitim işlem hattını çalıştırmak istemiyorsanız, bizim sağladığımız modeli kolayca kullanabilirsiniz. Modelinizi bir model kayıt defterinde saklamanın en güzel yönlerinden biri, paylaşılabilirlik ve erişilebilirliktir.

İş mikro hizmetini FastAPI kullanarak uygulayacağız çünkü popüler, kullanımı kolay ve hızlıdır. LLM mikro hizmeti AWS SageMaker'da dağıtılacak ve burada Hugging Face'in Deep Learning Containers (DLCs) ile entegrasyonundan yararlanacağız. DLC'leri sonraki bölümde tartışacağız, ancak sezgisel olarak, hizmet verme zamanında LLM'leri optimize etmek için kullanılan bir çıkarım motorudur (inference engine). İstem izleme işlem hattı Comet kullanılarak uygulanır, ancak bu modüle yalnızca 11. Bölüm'de göz atacağız.

SageMaker Çıkarım dağıtımı, size nasıl uygulanacağını göstereceğimiz aşağıdaki bileşenlerden oluşur:
- **SageMaker endpoint**: Bir endpoint, SageMaker'ın dağıtılan modellerden gerçek zamanlı tahminler sağlamak için barındırdığı ölçeklenebilir ve güvenli bir API'dir. Temelde uygulamaların modelinizle etkileşime girdiği arayüzdür. Dağıtıldıktan sonra, bir uygulama endpoint'e HTTP istekleri yaparak gerçek zamanlı tahminler alabilir.
- **SageMaker model**: SageMaker'da bir model, bir algoritmayı eğitmenin sonucudur. Ağırlıklar ve hesaplama mantığı dahil olmak üzere tahminler yapmak için gereken bilgileri içerir. Birden fazla model oluşturabilir ve bunları farklı konfigürasyonlarda veya çeşitli tahminler için kullanabilirsiniz.
- **SageMaker konfigürasyonu**: Bu konfigürasyon, modeli barındırmak için donanım ve yazılım kurulumunu belirtir. Endpoint için gereken kaynakları, makine öğrenimi hesaplama örneklerinin türü ve sayısı gibi tanımlar. Endpoint konfigürasyonları, bir endpoint oluştururken veya güncellerken kullanılır. Barındırılan modellerin dağıtımında ve ölçeklenebilirliğinde esneklik sağlar.
- **SageMaker Çıkarım bileşeni**: Bu, modeli ve konfigürasyonu bir endpoint'e bağlayan son parçadır. Bir endpoint'e birden fazla model dağıtabilirsiniz, her biri kendi kaynak konfigürasyonu ile. Dağıtıldıktan sonra modellere Python'da InvokeEndpoint API aracılığıyla kolayca erişilebilir.

Bu bileşenler birlikte, SageMaker'da makine öğrenimi modellerini dağıtmak ve yönetmek için sağlam bir altyapı oluşturur, ölçeklenebilir, güvenli ve verimli gerçek zamanlı tahminler sağlar.

Kod Açıklaması:

1. `SageMaker endpoint` oluşturma:
   ```python
   import sagemaker
   from sagemaker import get_execution_role

   sagemaker_session = sagemaker.Session()
   role = get_execution_role()

   # Endpoint konfigürasyonunu tanımlama
   endpoint_config_name = 'LLM-Twin-Endpoint-Config'
   sagemaker_session.create_endpoint_config(
       endpoint_config_name,
       {'InstanceType': 'ml.m5.xlarge', 'InitialInstanceCount': 1}
   )

   # Endpoint oluşturma
   endpoint_name = 'LLM-Twin-Endpoint'
   sagemaker_session.create_endpoint(
       endpoint_name=endpoint_name,
       config_name=endpoint_config_name
   )
   ```

   - Yukarıdaki kod, SageMaker'da bir endpoint oluşturur. Önce bir endpoint konfigürasyonu tanımlar ve daha sonra bu konfigürasyonu kullanarak endpoint'i oluşturur.

2. `SageMaker model` oluşturma:
   ```python
   # Modeli SageMaker'a yükleme
   model_name = 'LLM-Twin-Model'
   sagemaker_session.create_model(
       name=model_name,
       role=role,
       container_defs={
           'Image': '763104351884.dkr.ecr.us-west-2.amazonaws.com/huggingface-pytorch-inference:1.12.0-transformers4.20.1-gpu-py38-cu116-ubuntu20.04',
           'ModelDataUrl': 's3://your-bucket/model.tar.gz'
       }
   )
   ```

   - Bu kod, eğitilmiş modeli SageMaker'a yükler. Modelin Docker konteyner görüntüsünü ve model verilerinin S3 URL'sini tanımlar.

3. `SageMaker konfigürasyonu` tanımlama:
   - Yukarıdaki `SageMaker endpoint` oluşturma kodunda gösterildiği gibi, endpoint konfigürasyonu, instance tipi ve başlangıç instance sayısı gibi parametreleri tanımlar.

4. `SageMaker Çıkarım bileşeni` oluşturma:
   - Bu, endpoint'e model dağıtımı sırasında örtülü olarak yapılır. Yukarıdaki `SageMaker endpoint` ve `SageMaker model` oluşturma adımları, çıkarım bileşenini oluşturmak için gereken adımlardır.

Bu kod parçaları, SageMaker'da gerçek zamanlı çıkarım için gerekli olan temel bileşenleri oluşturmayı gösterir. Gerçek kullanım durumunda, modelinize, veri konumunuza ve diğer gereksinimlerinize göre bu kodları uyarlamanız gerekecektir.

---

