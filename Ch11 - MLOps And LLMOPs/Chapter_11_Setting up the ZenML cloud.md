# ZenML Bulut ve AWS Altyapısının Kurulumu

ZenML bulut ve AWS altyapısının kurulumu çok adımlı bir işlemdir. İlk olarak, bir ZenML bulut hesabı oluşturacağız, ardından ZenML bulut üzerinden AWS altyapısını kuracağız ve son olarak kodumuzu bir Docker imajına (Docker image) paketleyerek AWS SageMaker'da çalıştıracağız.

## ZenML Bulut Kurulumu

ZenML bulut kurulumuna başlamak için: https://cloud.zenml.io adresine gidin ve bir hesap oluşturun. 7 günlük ücretsiz deneme sürümü sunuyorlar, bu bizim örneklerimizi çalıştırmak için yeterli. Onboarding formunu doldurun ve "twin" adında bir organizasyon ve tenant (kiracı) oluşturun. Bir tenant, ZenML'in tamamen izole bir ortamda dağıtımı anlamına gelir (tenant refers to a deployment of ZenML in a fully isolated environment). Sonraki adıma geçmeden önce tenant sunucunuzun çalışmasını birkaç dakika bekleyin. İsterseniz, ZenML bulutunun nasıl çalıştığını daha basit bir örnekle anlamak için Hızlı Başlangıç Kılavuzu'nu (Quickstart Guide) inceleyebilirsiniz. LLM Twin uygulamasını dağıtmak için buna gerek olmasa da, her şeyin düzgün çalıştığından emin olmak için önerilir. Bu noktada, Hızlı Başlangıç Kılavuzu'nu incelediğinizi varsayıyoruz. Aksi takdirde, sonraki adımlarda sorunlarla karşılaşabilirsiniz.

### Kod:
```bash
zenml connect --url https://0c37a553-zenml.cloudinfra.zenml.io
```
### Açıklama:
- `zenml connect` komutu, projenizi ZenML bulut tenant'ınıza bağlar.
- `--url` parametresi, ZenML bulut tenant'ınızın URL'sini belirtir.

Projenizi bu ZenML bulut tenant'ına bağlamak için, projeye geri dönün ve dashboard'da sağlanan `zenml connect` komutunu çalıştırın. Komut, aşağıdaki örneğe benzer, ancak farklı bir URL ile:
Her şeyin düzgün çalıştığından emin olmak için, kodunuzdan rastgele bir pipeline çalıştırın. Bu noktada, hala yerel olarak çalıştırıyoruz, ancak sonuçları yerel sunucuya kaydetmek yerine bulut sürümüne kaydediyoruz:
### Kod:
```bash
poetry poe run-digital-data-etl
```
### Açıklama:
- `poetry poe run-digital-data-etl` komutu, `run-digital-data-etl` pipeline'unu çalıştırır.

ZenML dashboard'unun sol panelindeki Pipelines bölümüne gidin. Her şey düzgün çalıştıysa, 5. Adımda çalıştırdığınız pipeline'u orada görmelisiniz.

ZenML sunucu sürümünüzün yerel ZenML sürümünüzle eşleştiğinden emin olun. Örneğin, bu kitabı yazarken, her ikisi de 0.64.0 sürümündeydi. Eşleşmezlerse, garip davranışlarla karşılaşabilir veya düzgün çalışmayabilir. En kolay düzeltme, `pyproject.toml` dosyasını açmak, ZenML bağımlılığını bulmak ve sunucunuzun sürümüyle güncellemektir. Ardından, yerel sanal ortamınızı güncellemek için `poetry lock --no-update && poetry install` komutunu çalıştırın.

## AWS Stack Kurulumu

Kodunuzu AWS'ye göndermek için, bir ZenML stack oluşturmalısınız. Bir stack, ZenML'in pipeline'ları çalıştırmak için arka planda ihtiyaç duyduğu bileşenler kümesidir (örneğin, altta yatan orkestratör, nesne depolama ve konteyner kayıt defteri). Sezgisel olarak, stack'inizi altyapınız olarak görebilirsiniz. Yerel olarak çalışırken, ZenML hızlı bir şekilde kod geliştirmek ve yerel olarak test etmek için varsayılan bir stack sunar. Ancak, farklı stack'ler tanımlayarak, yerel ve AWS çalıştırmaları gibi farklı altyapı ortamları arasında hızlı bir şekilde geçiş yapabilirsiniz.

### Kod:
```bash
# Stack oluşturma adımları ZenML dashboard üzerinden yapılır.
# 1. Sol panelde Stacks bölümüne gidin ve New Stack butonuna tıklayın.
# 2. Create New Infrastructure → In-browser Experience'ı seçin.
# 3. AWS'i bulut sağlayıcınız olarak seçin.
# 4. Europe (Frankfurt)—eu-central-1'i konum olarak seçin (veya MongoDB ve Qdrant'ı kurduğunuz bölge).
# 5. Stack'inizi "aws-stack" olarak adlandırın.
```
### Açıklama:
- ZenML dashboard üzerinden yeni bir stack oluşturulur.
- "aws-stack" adıyla bir stack oluşturmak, sonraki komutların çalışması için gereklidir.

ZenML, IAM rollerini oluşturarak diğer bileşenlerin birbirleriyle iletişim kurmasına izin verecek, artifact depolama olarak bir S3 kovası (S3 bucket), konteyner kayıt defteri olarak bir ECR deposu (ECR repository) ve orkestratör olarak SageMaker oluşturacaktır.

### Kod:
```bash
# CloudFormation sayfasında, alt kısımda tüm kutuları işaretleyin ve Create stack butonuna tıklayın.
```
### Açıklama:
- CloudFormation, ZenML tarafından AWS kaynaklarını oluşturmak için kullanılan bir altyapı olarak kod (IaC) aracıdır.

AWS CloudFormation'ın tüm kaynakları oluşturmasını birkaç dakika bekleyin. ZenML sayfasına geri dönün ve Finish butonuna tıklayın.

## Oluşturulan AWS Kaynakları

- **IAM Rolü (IAM Role):** AWS hizmetlerine erişim izni vermek için kullanılan bir AWS kimliğidir.
- **S3:** Ölçeklenebilir ve güvenli nesne depolama hizmetidir.
- **ECR:** Tamamen yönetilen bir Docker konteyner kayıt defteridir.
- **SageMaker:** Geliştiricilerin ve veri bilimcilerin makine öğrenimi modellerini hızlı bir şekilde oluşturmalarını, eğitmelerine ve dağıtmalarına olanak tanıyan tamamen yönetilen bir hizmettir.
- **CloudFormation:** AWS kaynaklarını modellemeye ve kurmaya olanak tanıyan bir hizmettir.

## Kodun Konteynerleştirilmesi

ML pipeline'larını çalıştırmadan önce, son adım kodunuzu konteynerleştirmek ve bağımlılıklarınızı ve kodunuzu paketleyen bir Docker imajı hazırlamaktır.

### İngilizce Teknik Terimlerin Türkçeleri ve Parantez İçinde İngilizce Karşılıkları

- ZenML Bulut (ZenML Cloud)
- Altyapı (Infrastructure)
- Docker İmaji (Docker Image)
- Pipeline (Pipeline)
- Stack (Stack)
- IAM Rolü (IAM Role)
- S3 Kovası (S3 Bucket)
- ECR Deposu (ECR Repository)
- SageMaker (SageMaker)
- CloudFormation (CloudFormation)
- Konteyner Kayıt Defteri (Container Registry)
- Orkestratör (Orchestrator)
- Artifact Depolama (Artifact Storage)

---

