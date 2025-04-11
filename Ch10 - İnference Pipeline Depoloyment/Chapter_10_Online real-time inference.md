# Gerçek Zamanlı Çıkarım (Real-time Inference)

Gerçek zamanlı çıkarımda (real-time inference), HTTP istekleri (HTTP requests) aracılığıyla erişilebilen bir sunucuya (server) dayanan basit bir mimari (architecture) vardır. En popüler seçenekler, bir REST API veya gRPC sunucusu (gRPC server) uygulamaktır. REST API, istemci (client) ve sunucu arasında veri geçirmek için JSON kullandığından daha erişilebilir ancak daha yavaştır. Bu yaklaşım genellikle modelleri dahili ağınızın dışındaki daha geniş kitlelere hizmet ederken kullanılır. Örneğin, OpenAI'ın API'si bir REST API protokolü (REST API protocol) uygular. Öte yandan, bir gRPC uygulamak ML sunucunuzu (ML server) daha hızlı hale getirir, ancak esnekliğini ve genel uygulanabilirliğini azaltabilir. İstemci uygulamanızda protobuf şemaları (protobuf schemas) uygulamanız gerekir, bunlar JSON yapıları (JSON structures) ile çalışmaktan daha zahmetlidir. Ancak faydası, protobuf nesnelerinin (protobuf objects) baytlara (bytes) derlenebilmesi ve böylece ağ transferlerini (network transfers) çok daha hızlı hale getirmesidir. Bu nedenle, bu protokol genellikle aynı ML sistemi içindeki dahili hizmetler (internal services) için benimsenir.

Gerçek zamanlı çıkarım yaklaşımını (real-time inference approach) kullanarak, istemci ML hizmetine (ML service) bir HTTP isteği (HTTP request) gönderir, bu hemen isteği işler ve sonucu aynı yanıtta (response) döndürür. Bu eşzamanlı etkileşim (synchronous interaction), istemcinin ilerlemeden önce sonucun gelmesini beklemesi gerektiği anlamına gelir. Bunun verimli çalışması için altyapının (infrastructure) düşük gecikmeli (low-latency), yüksek yanıt verebilen ML hizmetlerini desteklemesi gerekir, genellikle hızlı, ölçeklenebilir sunucularda (scalable servers) dağıtılır. Gelen trafiği eşit olarak dağıtmak için yük dengeleme (load balancing) çok önemlidir, aynı zamanda otomatik ölçeklendirme (autoscaling) sistemin değişen yükleri işleyebilmesini sağlar. Yüksek erişilebilirlik (high availability) de hizmetin her zaman çalışır durumda kalması için önemlidir.

Örneğin, bu mimari genellikle LLM'ler (LLMs) ile etkileşimde bulunurken mevcuttur, örneğin bir sohbet robotuna (chatbot) veya API'ye (API) (LLM'ler tarafından desteklenmektedir) bir istek gönderdiğinizde, tahminleri hemen alırsınız. ChatGPT veya Claude gibi LLM hizmetleri (LLM services), her bir jetonu (token) son kullanıcıya ayrı ayrı akıtmak (stream) için WebSockets'i (WebSockets) kullanır, böylece etkileşimi daha duyarlı hale getirir. Diğer ünlü örnekler, retrieval-augmented generation (RAG) veya çevrimiçi öneri motorları (online recommendation engines) için kullanılan embedding veya reranking modelleri (embedding or reranking models) gibi AI hizmetleridir (AI services). Gerçek zamanlı çıkarımın basitliği, doğrudan istemci-sunucu etkileşimi (client-server interaction) ile, sohbet robotları (chatbots) veya gerçek zamanlı öneriler (real-time recommendations) gibi anında yanıtlar gerektiren uygulamalar için çekici bir seçenek haline getirir. Ancak, bu yaklaşım ölçeklendirilmesi zor olabilir ve düşük trafik dönemlerinde kaynakların eksik kullanılmasına yol açabilir.

**Kod Açıklaması**

Bu metinde doğrudan bir kod pasajı bulunmamaktadır. Ancak, gerçek zamanlı çıkarım (real-time inference) için kullanılan bazı teknik terimlerin ve protokollerin açıklaması aşağıda verilmiştir:

*   REST API: REST (Representational State of Resource) API, istemci ve sunucu arasında veri alışverişi yapmak için kullanılan bir uygulama programlama arayüzüdür (API). JSON (JavaScript Object Notation) veri formatını kullanır.
*   gRPC: gRPC, Google tarafından geliştirilen bir RPC (Remote Procedure Call) çerçevesidir. Protobuf (Protocol Buffers) veri formatını kullanır ve yüksek performanslı iletişim sağlar.
*   Protobuf Şemaları: Protobuf şemaları, veri yapılarını tanımlamak için kullanılan bir dildir. Protobuf nesneleri, baytlara derlenerek hızlı veri transferi sağlar.
*   Load Balancing (Yük Dengeleme): Gelen trafiği birden fazla sunucuya dağıtarak sistemin daha verimli çalışmasını sağlar.
*   Autoscaling (Otomatik Ölçeklendirme): Sistemin değişen yükleri karşılayabilmesi için otomatik olarak kaynak eklemeyi veya kaldırmayı sağlar.
*   WebSockets: WebSockets, istemci ve sunucu arasında çift yönlü, gerçek zamanlı iletişim kurmak için kullanılan bir protokoldür.

**Örnek Kod**

Aşağıda basit bir REST API örneği verilmiştir (Python ve Flask kullanılarak):

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

# Örnek model
model = {
    "name": "example_model",
    "version": "1.0"
}

# Tahmin yapmak için örnek bir fonksiyon
def make_prediction(data):
    # Burada gerçek modelin tahmin yapması gerekir
    return {"prediction": "example_prediction"}

@app.route('/predict', methods=['POST'])
def predict():
    data = request.get_json()
    prediction = make_prediction(data)
    return jsonify(prediction)

if __name__ == '__main__':
    app.run(debug=True)
```

Bu kod, `/predict` endpoint'ine gelen POST isteklerini işler ve bir tahmin yapar. Yanıt olarak JSON formatında bir çıktı verir.

gRPC için örnek bir kod (Python ve gRPC kullanılarak):

```python
import grpc
from concurrent import futures

# Protobuf dosyasını import edin
import example_pb2
import example_pb2_grpc

class ExampleService(example_pb2_grpc.ExampleServicer):
    def MakePrediction(self, request, context):
        # Burada gerçek modelin tahmin yapması gerekir
        prediction = example_pb2.Prediction(prediction="example_prediction")
        return prediction

def serve():
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    example_pb2_grpc.add_ExampleServicer_to_server(ExampleService(), server)
    server.add_insecure_port('[::]:50051')
    server.start()
    server.wait_for_termination()

if __name__ == '__main__':
    serve()
```

Bu kod, gRPC sunucusu kurar ve `/MakePrediction` metodunu uygular. Protobuf nesneleri kullanarak veri alışverişi yapar.

---

