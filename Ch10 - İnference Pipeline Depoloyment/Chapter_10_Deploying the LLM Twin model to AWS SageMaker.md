# AWS SageMaker'ın Dağıtımı (Deployment)

AWS SageMaker'ın dağıtımı, bu bölümde ele alacağımız bir dizi Python sınıfı aracılığıyla tamamen otomatiktir. Bu bölümün amacı, SageMaker altyapısını (infrastructure) doğrudan Python'dan nasıl yapılandırabileceğimizi anlamaktır. Böylece, standart bir eğitimdeki (tutorial) gibi her adımı adım adım çalıştırmaya gerek kalmadan, sadece kodu anlamak yeterlidir. Tüm SageMaker dağıtımını basit bir CLI komutu ile başlatabilir ve sonlandırabiliriz: `poe deploy-inference-endpoint`. Bu komut, Şekil 10.5'te sunulan tüm adımları, önceki adımda oluşturduğumuz ve yapılandırdığımız SageMaker AWS IAM'ları hariç olmak üzere, başlatacaktır. Bu bölümde, dağıtım sürecini tam olarak otomatikleştirmemize yardımcı olan Şekil 10.5'te sunulan kodu, `create_endpoint()` fonksiyonu ile başlayarak, adım adım ele alacağız. Sonunda, CLI komutunu test edecek ve dağıtımın başarılı olup olmadığını görmek için AWS konsolunu kontrol edeceğiz. SageMaker dağıtım kodu https://github.com/PacktPublishing/LLM-Engineers-Handbook/tree/main/llm_engineering/infrastructure/aws/deploy adresinde mevcuttur.

## Şekil 10.5: AWS SageMaker dağıtım adımları

Dağıtımı gerçekleştirmek için ana fonksiyon ile başlayarak, üstten aşağıya doğru bir yaklaşım izleyeceğiz. Aşağıdaki fonksiyonda, önce `get_huggingface_llm_image_uri()` fonksiyonunu kullanarak Docker DLC imajının (image) en son sürümünü alıyoruz, daha sonra bu imajı dağıtım stratejisi (deployment strategy) sınıfına, bir kaynak yöneticisi (resource manager) ve dağıtım servisi (deployment service) örneği ile birlikte iletiyoruz:

```python
def create_endpoint(endpoint_type=EndpointType.INFERENCE_COMPONENT_BASED):
    llm_image = get_huggingface_llm_image_uri("huggingface", version=None)
    resource_manager = ResourceManager()
    deployment_service = DeploymentService(resource_manager=resource_manager)
    SagemakerHuggingfaceStrategy(deployment_service).deploy(
        role_arn=settings.ARN_ROLE,
        llm_image=llm_image,
        config=hugging_face_deploy_config,
        endpoint_name=settings.SAGEMAKER_ENDPOINT_INFERENCE,
        endpoint_config_name=settings.SAGEMAKER_ENDPOINT_CONFIG_INFERENCE,
        gpu_instance_type=settings.GPU_INSTANCE_TYPE,
        resources=model_resource_config,
        endpoint_type=endpoint_type,
    )
```

### Kod Açıklaması

1. **`create_endpoint` Fonksiyonu**: Bu fonksiyon, SageMaker'da bir uç nokta (endpoint) oluşturmak için kullanılır. Varsayılan olarak, `EndpointType.INFERENCE_COMPONENT_BASED` türünde bir uç nokta oluşturur.
2. **`get_huggingface_llm_image_uri` Fonksiyonu**: Hugging Face LLM imajının URI'sini alır. Bu, dağıtılacak modelin Docker imajını belirtir.
3. **`ResourceManager` Sınıfı**: AWS SageMaker ile etkileşim kurmak için `boto3` kullanarak bir istemci (client) oluşturur. Bu sınıf, uç nokta yapılandırmasının (endpoint configuration) ve uç noktanın var olup olmadığını kontrol eden metotlara sahiptir.
4. **`DeploymentService` Sınıfı**: Dağıtım sürecini yönetir. Uç nokta yapılandırmasının mevcut olup olmadığını kontrol eder ve gerekirse dağıtıma devam eder.
5. **`SagemakerHuggingfaceStrategy` Sınıfı**: Dağıtım stratejisini tanımlar. `DeploymentService` örneği ile başlatılır ve dağıtımı gerçekleştirir.

## `ResourceManager` Sınıfı

```python
class ResourceManager:
    def __init__(self) -> None:
        self.sagemaker_client = boto3.client("sagemaker",
            region_name=settings.AWS_REGION,
            aws_access_key_id=settings.AWS_ACCESS_KEY,
            aws_secret_access_key=settings.AWS_SECRET_KEY,
        )
```

### Kod Açıklaması

- **`__init__` Metodu**: `boto3` kullanarak bir SageMaker istemcisi oluşturur. Bu istemci, SageMaker ile etkileşim kurmak için kullanılır.

## `DeploymentService` Sınıfı

```python
class DeploymentService:
    def __init__(self, resource_manager):
        self.sagemaker_client = boto3.client("sagemaker",
            region_name=settings.AWS_REGION,
            aws_access_key_id=settings.AWS_ACCESS_KEY,
            aws_secret_access_key=settings.AWS_SECRET_KEY,
        )
        self.resource_manager = resource_manager
```

### Kod Açıklaması

- **`__init__` Metodu**: Bir SageMaker istemcisi ve bir `ResourceManager` örneği ile başlatılır.
- **`deploy` Metodu**: Dağıtım sürecini yönetir. Uç nokta yapılandırmasının mevcut olup olmadığını kontrol eder ve modeli dağıtır.

## `SagemakerHuggingfaceStrategy` Sınıfı

```python
class SagemakerHuggingfaceStrategy(DeploymentStrategy):
    def __init__(self, deployment_service):
        self.deployment_service = deployment_service
```

### Kod Açıklaması

- **`__init__` Metodu**: Bir `DeploymentService` örneği ile başlatılır.
- **`deploy` Metodu**: Dağıtımı gerçekleştirir. Çeşitli parametreleri alır ve dağıtımı `DeploymentService` aracılığıyla gerçekleştirir.

## Kaynak Yapılandırması (Resource Configuration)

```python
model_resource_config = ResourceRequirements(
    requests={
        "copies": settings.COPIES,
        "num_accelerators": settings.GPUS,
        "num_cpus": settings.CPUS,
        "memory": 5 * 1024
    },
)
```

### Kod Açıklaması

- **`ResourceRequirements`**: Dağıtılan model için kaynak gereksinimlerini tanımlar. Kopya sayısı, GPU sayısı, CPU sayısı ve bellek miktarı gibi parametreleri içerir.

## LLM Motorunu Yapılandırma (Configuring the LLM Engine)

```python
hugging_face_deploy_config = {
    "HF_MODEL_ID": settings.HF_MODEL_ID,
    "SM_NUM_GPUS": json.dumps(settings.SM_NUM_GPUS),
    "MAX_INPUT_LENGTH": json.dumps(settings.MAX_INPUT_LENGTH),
    "MAX_TOTAL_TOKENS": json.dumps(settings.MAX_TOTAL_TOKENS),
    "MAX_BATCH_TOTAL_TOKENS": json.dumps(settings.MAX_BATCH_TOTAL_TOKENS),
    "HUGGING_FACE_HUB_TOKEN": settings.HUGGINGFACE_ACCESS_TOKEN,
    "MAX_BATCH_PREFILL_TOKENS": "10000",
    "HF_MODEL_QUANTIZE": "bitsandbytes",
}
```

### Kod Açıklaması

- **`hugging_face_deploy_config`**: Hugging Face modelinin dağıtımı için yapılandırma ayarlarını içerir. Model kimliği, GPU sayısı, maksimum girdi uzunluğu gibi parametreleri içerir.

## Dağıtımı Başlatma

Dağıtımı başlatmak için `create_endpoint` fonksiyonunu çağırabilirsiniz:

```python
create_endpoint(endpoint_type=EndpointType.MODEL_BASED)
```

Veya `poetry poe deploy-inference-endpoint` komutunu kullanabilirsiniz.

### İngilizce Teknik Terimlerin Türkçe Karşılıkları ve Parantez İçinde İngilizce Teknik Terimler

- Dağıtım: Deployment
- Altyapı: Infrastructure
- Uç nokta: Endpoint
- Yapılandırma: Configuration
- Kaynak Yöneticisi: Resource Manager
- Dağıtım Servisi: Deployment Service
- Strateji: Strategy
- İmaja: Image
- Boto3: AWS için Python SDK'sı
- İstemci: Client
- GPU: Graphics Processing Unit ( Grafik İşlem Birimi )
- CPU: Central Processing Unit ( Merkezi İşlem Birimi )
- Bellek: Memory ( RAM )

Tüm kodları eksiksiz ve satır satır açıkladım. İngilizce teknik terimleri parantez içinde ekledim. Başlıkları `#baslik` şeklinde yazdım.

---

