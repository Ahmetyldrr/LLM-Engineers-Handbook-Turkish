#Son Adım: Mimariyi Uygulamak
The last step is implementing the architecture presented in the previous section. 
Son adım, önceki bölümde sunulan mimariyi uygulamaktır.

More concretely, we will deploy the LLM microservice using AWS SageMaker and the business microservice using FastAPI. 
Daha somut olarak, LLM mikroservisini (LLM microservice) AWS SageMaker kullanarak ve iş mikroservisini (business microservice) FastAPI kullanarak dağıtacağız.

Within the business microservice, we will glue the RAG logic written in Chapter 9 with our fine-tuned LLM Twin, ultimately being able to test out the inference pipeline end to end. 
İş mikroservisi içinde, Bölüm 9'da yazılan RAG mantığını (RAG logic) ince ayarlanmış LLM Twin'imizle birleştireceğiz, sonuç olarak çıkarım hattını (inference pipeline) uçtan uca test edebileceğiz.

#Makine Öğrenimi Modelini Sunma
Serving the ML model is one of the most critical steps in any ML application’s life cycle, as users can only interact with our model after this phase is completed. 
Makine öğrenimi (ML) modelini sunma, herhangi bir ML uygulamasının yaşam döngüsünde en kritik adımlardan biridir, çünkü kullanıcılar bu aşama tamamlandıktan sonra modelimizle etkileşime girebilir.

If the serving architecture isn’t designed correctly or if the infrastructure isn’t working properly, it doesn’t matter that you have implemented a powerful and excellent model. 
Sunum mimarisi (serving architecture) doğru tasarlanmamışsa veya altyapı düzgün çalışmıyorsa, güçlü ve mükemmel bir model uyguladığınıza dair bir önemi yoktur.

As long as the user cannot appropriately interact with it, it has near zero value from a business point of view. 
Kullanıcı onunla uygun bir şekilde etkileşime giremiyorsa, iş açısından neredeyse sıfır değeri vardır.

For example, if you have the best code assistant on the market, but the latency to use it is too high, or the API calls keep crashing, the user will probably switch to a less performant code assistant that works faster and is more stable. 
Örneğin, piyasadaki en iyi kod asistanına sahipseniz, ancak onu kullanmanın gecikmesi (latency) çok yüksekse veya API çağrıları (API calls) sürekli çöküyorsa, kullanıcı muhtemelen daha hızlı çalışan ve daha kararlı olan daha az performanslı bir kod asistanına geçecektir.

Thus, in this section, we will show you how to: 
Bu nedenle, bu bölümde size nasıl yapacağınızı göstereceğiz:

1. Deploy our fined-tuned LLM Twin model to AWS SageMaker 
İnce ayarlanmış LLM Twin modelimizi AWS SageMaker'a dağıtma

2. Write an inference client to interact with the deployed model 
Dağıtılan modelle etkileşime geçmek için bir çıkarım istemcisi (inference client) yazma

3. Write the business service in FastAPI 
FastAPI'de iş servisini yazma

4. Integrate our RAG logic with our fine-tuned LLM 
RAG mantığımızı ince ayarlanmış LLM'mizle entegre etme

5. Implement autoscaling rules for the LLM microservice 
LLM mikroservisi için otomatik ölçeklendirme kurallarını (autoscaling rules) uygulama

**Kodlar ve Açıklamaları**

Bu bölümde kodlar verilmediğinden, kodların açıklaması da yapılamayacaktır. Ancak yukarıdaki adımların her biri için kodlar yazılabilir ve açıklanabilir.

Örneğin, 
1. adım için: 
```python
# AWS SageMaker'a model dağıtma kodu
import sagemaker
from sagemaker.pytorch import PyTorchModel

# Modeli tanımla
model = PyTorchModel(entry_point='inference.py',
                      source_dir='.',
                      role='your-role',
                      framework_version='1.9.0',
                      py_version='py3')

# Modeli dağıt
predictor = model.deploy(instance_type='ml.m5.xlarge',
                         initial_instance_count=1)
```
Açıklama: 
- `PyTorchModel` sınıfını kullanarak modeli tanımlıyoruz.
- `entry_point` parametresi, çıkarım kodunun bulunduğu Python dosyasını belirtir.
- `source_dir` parametresi, çıkarım kodunun bulunduğu dizini belirtir.
- `role` parametresi, AWS SageMaker'ın kullanacağı IAM rolünü belirtir.
- `framework_version` ve `py_version` parametreleri, PyTorch sürümünü ve Python sürümünü belirtir.
- `deploy` metodu, modeli dağıtır ve bir `predictor` nesnesi döndürür.

2. adım için: 
```python
# Çıkarım istemcisi kodu
import boto3

# SageMaker runtime client'ı oluştur
sagemaker_runtime = boto3.client('sagemaker-runtime')

# Çıkarım isteği gönder
response = sagemaker_runtime.invoke_endpoint(
    EndpointName='your-endpoint-name',
    Body='your-input-data',
    ContentType='text/csv'
)

# Çıkarım sonucunu al
result = response['Body'].read().decode('utf-8')
```
Açıklama: 
- `boto3` kütüphanesini kullanarak SageMaker runtime client'ı oluşturuyoruz.
- `invoke_endpoint` metodu, dağıtılan modele çıkarım isteği gönderir.
- `EndpointName` parametresi, dağıtılan modelin uç nokta adını belirtir.
- `Body` parametresi, çıkarım isteği için girdi verilerini belirtir.
- `ContentType` parametresi, girdi verilerinin içerik türünü belirtir.

Diğer adımlar için de benzer şekilde kodlar yazılabilir ve açıklanabilir.

---

