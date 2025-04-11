# LLM Servisini Çağırma

Şimdi LLM (Large Language Model) servisi AWS SageMaker'da dağıtıldığına göre, servisi nasıl çağıracağımızı öğrenelim. Bunu yapmak için, SageMaker için prompt'u hazırlamamıza, HTTP istekleri aracılığıyla çıkarım (inference) endpoint'ini çağırmamıza ve sonuçları istemci (client) tarafından işlenebilecek bir şekilde çözmemize yardımcı olacak iki sınıf yazacağız. Tüm AWS SageMaker Çıkarım (Inference) kodu GitHub'da llm_engineering/model/inference adresinde mevcuttur. 
Her şey aşağıdaki örnekle başlar: 
```python
text = "Write me a post about AWS SageMaker inference endpoints."
llm = LLMInferenceSagemakerEndpoint(
    endpoint_name=settings.SAGEMAKER_ENDPOINT_INFERENCE
)
Answer = InferenceExecutor(llm, text).execute()
```
Daha önce olduğu gibi, size LLMInferenceSagemakerEndpoint ve InferenceExecutor sınıflarını anlatacağız. SageMaker ile doğrudan etkileşime giren LLMInferenceSagemakerEndpoint sınıfıyla başlayalım. Yapıcı (constructor), SageMaker endpoint'i ile etkileşim için gerekli tüm temel özellikleri başlatır:
```python
class LLMInferenceSagemakerEndpoint(Inference):
    def __init__(self, endpoint_name: str, default_payload: Optional[Dict[str, Any]] = None, inference_component_name: Optional[str] = None) -> None:
        super().__init__()
        self.client = boto3.client("sagemaker-runtime", region_name=settings.AWS_REGION, aws_access_key_id=settings.AWS_ACCESS_KEY, aws_secret_access_key=settings.AWS_SECRET_KEY)
        self.endpoint_name = endpoint_name
        self.payload = default_payload if default_payload else self._default_payload()
        self.inference_component_name = inference_component_name
```
endpoint_name, çağırmak istediğimiz SageMaker endpoint'ini tanımlamak için çok önemlidir. Ayrıca, yöntem (method), sağlanan bir değer kullanılarak veya hiçbiri sağlanmamışsa varsayılan bir yük (payload) oluşturan bir yöntem çağrılarak yükü başlatır.

Sınıfın temel özelliklerinden biri, çıkarım istekleri için varsayılan bir yük oluşturma yeteneğidir. Bu, `_default_payload()` yöntemi tarafından işlenir:
```python
def _default_payload(self) -> Dict[str, Any]:
    return {
        "inputs": "",
        "parameters": {
            "max_new_tokens": settings.MAX_NEW_TOKENS_INFERENCE,
            "top_p": settings.TOP_P_INFERENCE,
            "temperature": settings.TEMPERATURE_INFERENCE,
            "return_full_text": False,
        },
    }
```
Bu yöntem, çıkarım için gönderilecek yükün varsayılan yapısını temsil eden bir sözlük (dictionary) döndürür. Parametreler bölümü, oluşturulacak token sayısını, örnekleme stratejisini (`top_p`), ve çıktının rastgeleliğini kontrol eden sıcaklık ayarını içerir. Bu parametreler, farklı çıkarım görevlerinde tutarlılığı sağlamak için uygulamanın ayarlarından alınır.

Sınıf, `set_payload()` yöntemi aracılığıyla yükün özelleştirilmesine olanak tanır, bu da kullanıcının çıkarım isteği göndermeden önce girdileri ve parametreleri değiştirmesini sağlar:
```python
def set_payload(self, inputs: str, parameters: Optional[Dict[str, Any]] = None) -> None:
    self.payload["inputs"] = inputs
    if parameters:
        self.payload["parameters"].update(parameters)
```
Bu yöntem, yükün girdiler bölümünü kullanıcı tarafından sağlanan yeni metinle günceller. Ayrıca, sağlanan herhangi bir çıkarım parametresi varsa, bunları değiştirmeye de olanak tanır.

Son olarak, `inference()` yöntemini kullanarak SageMaker endpoint'ini özelleştirilmiş yükle çağırırız:
```python
def inference(self) -> Dict[str, Any]:
    try:
        logger.info("Inference request sent.")
        invoke_args = {
            "EndpointName": self.endpoint_name,
            "ContentType": "application/json",
            "Body": json.dumps(self.payload),
        }
        if self.inference_component_name not in ["None", None]:
            invoke_args["InferenceComponentName"] = self.inference_component_name
        response = self.client.invoke_endpoint(**invoke_args)
        response_body = response["Body"].read().decode("utf8")
        return json.loads(response_body)
    except Exception:
        logger.exception("SageMaker inference failed.")
        raise
```
Bu yöntemde, `inference()` yöntemi SageMaker endpoint'ine gönderilecek isteği oluşturur. Yöntem, yükü ve diğer gerekli ayrıntıları SageMaker'ın beklediği bir formata paketler. Bir `inference_component_name` belirtilmişse, istekte bulunur, böylece gerektiğinde çıkarım süreci üzerinde daha ayrıntılı kontrol sağlar. İstek, `invoke_endpoint()` işlevi kullanılarak gönderilir ve yanıt okunur, çözülür ve bir JSON nesnesi olarak döndürülür.

Şimdi, InferenceExecutor'un daha önce sunulan LLMInferenceSagemakerEndpoint sınıfını kullanarak AWS SageMaker endpoint'ine HTTP istekleri göndermek için nasıl kullandığını anlayalım. InferenceExecutor sınıfı, yapıcı (constructor) ile başlar, bu da LLM'yi çağırmak için anahtar parametreleri alır:
```python
class InferenceExecutor:
    def __init__(self, llm: Inference, query: str, context: str | None = None, prompt: str | None = None) -> None:
        self.llm = llm
        self.query = query
        self.context = context if context else ""
        if prompt is None:
            self.prompt = """You are a content creator. Write what the user asked you to while using the provided context as the primary source of information for the content. User query: {query} Context: {context}"""
        else:
            self.prompt = prompt
```
`execute()` yöntemi, InferenceExecutor sınıfının temel bileşenidir. Bu yöntem, aslında çıkarımı gerçekleştirmekle sorumludur. `execute()` çağrıldığında, kullanıcının sorgusunu ve içeriğini biçimlendirerek LLM'ye gönderilecek yükü hazırlar. Daha sonra, LLM'nin davranışını etkileyen çeşitli parametreleri yapılandırır, örneğin modelin oluşturmasına izin verilen yeni tokenların maksimum sayısını, tekrarlama cezası (repetition penalty) ve çıktının rastgeleliğini kontrol eden sıcaklık ayarını. Yük ve parametreler ayarlandıktan sonra, yöntem LLMInferenceSagemakerEndpoint'ten `inference()` işlevini çağırır ve oluşturulan yanıtı bekler:
```python
def execute(self) -> str:
    self.llm.set_payload(
        inputs=self.prompt.format(query=self.query, context=self.context),
        parameters={
            "max_new_tokens": settings.MAX_NEW_TOKENS_INFERENCE,
            "repetition_penalty": 1.1,
            "temperature": settings.TEMPERATURE_INFERENCE,
        },
    )
    answer = self.llm.inference()[0]["generated_text"]
    return answer
```
Inference arayüzünü (interface) uygulayan bir nesne aracılığıyla çıkarım yaparak, farklı çıkarım stratejilerini kolayca ekleyebiliriz ve yukarıda sunulan LLMInferenceSagemakerEndpoint uygulamasını kodun diğer bölümlerini değiştirmeden kullanabiliriz.

Bir test örneği çalıştırmak basittir. Aşağıda gösterildiği gibi aşağıdaki Python dosyasını çağırmanız yeterlidir:
```bash
poetry run python -m llm_engineering.model.inference.test
```
Ayrıca, kolaylık sağlamak için, bunu bir poe komutu altında sarıyoruz:
```bash
poetry poe test-sagemaker-endpoint
```
Şimdi, FastAPI kullanarak iş mikroservisini (business microservice) nasıl uyguladığımızı anlamalıyız. Bu mikroservis, yukarıda tanımlanan LLM mikroservisine HTTP istekleri gönderecek ve 9. Bölüm'de uygulanan RAG alma modülünü çağıracaktır.

### Kod Açıklaması

1. `LLMInferenceSagemakerEndpoint` sınıfı:
   - `__init__`: SageMaker endpoint'i ile etkileşim için gerekli özellikleri başlatır.
   - `_default_payload`: Çıkarım istekleri için varsayılan yükü oluşturur.
   - `set_payload`: Yükü özelleştirmek için girdileri ve parametreleri günceller.
   - `inference`: SageMaker endpoint'ine çıkarım isteği gönderir ve yanıtı işler.

2. `InferenceExecutor` sınıfı:
   - `__init__`: LLM'yi çağırmak için gerekli parametreleri alır.
   - `execute`: Çıkarımı gerçekleştirir, yükü hazırlar ve `inference()` işlevini çağırır.

### İngilizce Teknik Terimler (Parantez içinde)

- LLM (Large Language Model)
- SageMaker (AWS SageMaker)
- Çıkarım (Inference)
- Endpoint (Uç nokta)
- Payload (Yük)
- Parametreler (Parameters)
- Token (Sözcük birimi)
- Top_p (Örnekleme stratejisi)
- Sıcaklık (Temperature)
- Tekrarlama Cezası (Repetition Penalty)
- RAG (Retrieval-Augmented Generation)
- FastAPI (Hızlı API)
- Mikroservis (Microservice)
- HTTP (Hypertext Transfer Protocol)

---

