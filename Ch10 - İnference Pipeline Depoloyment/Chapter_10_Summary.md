#Bu Bölümde Neler Öğrendik

Bu bölümde, bir ML modelini (Makine Öğrenimi modeli) sunmadan önce, ister bir LLM (Büyük Dil Modeli) olsun ister olmasın, hangi tasarım kararlarının verileceğini, ML modelleri için üç temel dağıtım türünü (deployment types) size anlatarak öğrendik: çevrimiçi gerçek zamanlı çıkarım (online real-time inference), eşzamansız çıkarım (asynchronous inference) ve çevrimdışı toplu dönüştürme (offline batch transform). Ardından, ML-servis hizmetimizi bir monolit uygulama (monolith application) olarak oluşturmanın mantıklı olup olmadığını veya bir LLM mikroservisi (LLM microservice) ve bir iş mikroservisi (business microservice) gibi iki mikroservise ayırmanın mantıklı olup olmadığını değerlendirdik. Bunu yapmak için, model sunumunda (model-serving) monolitik (monolithic) ve mikroservisler (microservices) mimarisinin artılarını ve eksilerini tarttık. Ardından, ince ayarlı LLM Twin'imizi bir AWS SageMaker Çıkarım uç noktasına (Inference endpoint) dağıtma sürecini size anlattık. Ayrıca, FastAPI kullanarak iş mikroservisini nasıl uygulayacağımızı gördük, bu uygulama Chapter 9'da uygulanan alma modülü (retrieval module) temel alınarak RAG adımlarının tümünü ve AWS SageMaker'da dağıtılan LLM mikroservisini içerir. Son olarak, bir otomatik ölçeklendirme stratejisi (autoscaling strategy) neden uygulamamız gerektiğini araştırdık. Ayrıca, belirli bir ölçüt kümesine (metrics) göre ölçeklendirme yapan popüler bir otomatik ölçeklendirme stratejisini inceledik ve AWS SageMaker'da nasıl uygulanacağını gördük. Bir sonraki bölümde, MLOps ve LLMOps'un temellerini öğrenecek ve ardından ZenML ardışık düzenlerini (pipelines) AWS'ye dağıtmayı ve sürekli eğitim (continuous training), sürekli entegrasyon (continuous integration) ve sürekli teslimat (continuous delivery) (CT/CI/CD) ve izleme ardışık düzenini (monitoring pipeline) uygulamayı keşfedeceğiz.

İngilizce Teknik Terimlerin Türkçeleri ve Açıklamaları Parantez içinde verilmiştir.

Kodların Ayrıntılı Açıklaması:

Bu metinde kod bulunmamaktadır. 

Ancak, anlatılan konulara ait kod örnekleri aşağıdaki gibi olabilir:

*   AWS SageMaker Inference endpoint oluşturma kodu:
    ```python
import sagemaker
from sagemaker import get_execution_role

sagemaker_session = sagemaker.Session()
role = get_execution_role()

# Modeli SageMaker'da oluşturma
from sagemaker.pytorch import PyTorchModel
model = PyTorchModel(entry_point='inference.py',
                     source_dir='code',
                     role=role,
                     framework_version='1.9.0',
                     py_version='py38')

# Modeli dağıtma
predictor = model.deploy(instance_type='ml.m5.xlarge',
                          initial_instance_count=1)
```
    *   `sagemaker` kütüphanesini import eder ve bir SageMaker oturumu oluşturur.
    *   `get_execution_role()` fonksiyonu ile SageMaker'ın çalışacağı execution role'u alır.
    *   PyTorchModel sınıfını kullanarak modeli SageMaker'da oluşturur. `entry_point` parametresi, çıkarım (inference) kodunun bulunduğu Python dosyasını belirtir. `source_dir` parametresi, çıkarım kodunun bulunduğu dizini belirtir.
    *   Modeli `deploy` metodunu kullanarak dağıtır. `instance_type` parametresi, modelin dağıtılacağı instance tipini belirtir. `initial_instance_count` parametresi, başlangıçta kaç instance oluşturulacağını belirtir.

*   FastAPI kullanarak iş mikroservisini uygulama kodu:
    ```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Request(BaseModel):
    input_text: str

@app.post("/predict")
def predict(request: Request):
    # Çıkarım kodunu burada uygula
    output = llm_microservice.predict(request.input_text)
    return {"output": output}
```
    *   FastAPI kütüphanesini import eder ve bir FastAPI uygulaması oluşturur.
    *   `Request` sınıfını tanımlayarak, gelen isteğin yapısını belirtir.
    *   `/predict` endpoint'ini tanımlar ve gelen isteğe göre çıkarım yapar. `llm_microservice.predict` fonksiyonu, LLM mikroservisini çağırarak çıkarım yapar.

*   Otomatik ölçeklendirme stratejisi uygulama kodu (AWS SageMaker):
    ```python
import boto3

sagemaker_client = boto3.client('sagemaker')

# Otomatik ölçeklendirme yapılandırmasını oluşturma
response = sagemaker_client.register_scalable_target(
    ServiceNamespace='sagemaker',
    ResourceId='endpoint/your-endpoint-name/variant/your-variant-name',
    ScalableDimension='sagemaker:variant:DesiredInstanceCount',
    MinCapacity=1,
    MaxCapacity=10
)

# Ölçeklendirme politikasını oluşturma
response = sagemaker_client.put_scaling_policy(
    PolicyName='your-policy-name',
    ServiceNamespace='sagemaker',
    ResourceId='endpoint/your-endpoint-name/variant/your-variant-name',
    ScalableDimension='sagemaker:variant:DesiredInstanceCount',
    PolicyType='TargetTrackingScaling',
    TargetTrackingScalingConfiguration={
        'PredefinedMetricSpecification': {
            'PredefinedMetricType': 'SageMakerVariantInvocationsPerInstance'
        },
        'TargetValue': 100.0,
        'ScaleOutCooldown': 300,
        'ScaleInCooldown': 300
    }
)
```
    *   Boto3 kütüphanesini kullanarak SageMaker client'ı oluşturur.
    *   `register_scalable_target` metodunu kullanarak otomatik ölçeklendirme yapılandırmasını oluşturur. `MinCapacity` ve `MaxCapacity` parametreleri, instance sayısının minimum ve maksimum değerlerini belirtir.
    *   `put_scaling_policy` metodunu kullanarak ölçeklendirme politikasını oluşturur. `TargetTrackingScaling` politikası, belirli bir ölçütün hedef değerine göre ölçeklendirme yapar. `PredefinedMetricType` parametresi, ölçütün tipini belirtir (`SageMakerVariantInvocationsPerInstance`). `TargetValue` parametresi, hedef değeri belirtir. `ScaleOutCooldown` ve `ScaleInCooldown` parametreleri, ölçeklendirme işlemleri arasındaki bekleme sürelerini belirtir.

---

