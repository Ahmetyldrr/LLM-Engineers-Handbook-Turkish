# Embeddings (Gömme Vektörleri) Oluşturma

Embeddings (Gömme Vektörleri), girdiğiniz verinin bağlamını (context) ve anlambilimini (semantics) anlamak için derin öğrenme modelleri (deep learning models) tarafından oluşturulur ve bu verileri sürekli bir vektör uzayına (continuous vector space) projeler. Çeşitli derin öğrenme modelleri, veri girdi türüne (data input type) göre değişen embeddings oluşturmak için kullanılabilir. Bu nedenle, bir embedding model seçmeden önce verilerinizi ve ondan ne istediğinizi anlamak temeldir. Örneğin, metin verileriyle çalışırken, kelime dağarcığınız için embeddings oluşturmak üzere kullanılan ilk yöntemlerden biri Word2Vec ve GloVe'dir. Bunlar hala basit uygulamalar için kullanılan popüler yöntemlerdir. Diğer bir popüler yöntem, BERT gibi yalnızca kodlayıcı (encoder-only) transformatörleri veya RoBERTa gibi ailesinden diğer yöntemleri kullanmaktır. Bu modeller, girdiğiniz verileri daha sonra embeddings olarak kullanılabilecek yoğun bir vektör uzayına (dense vector space) akıllıca projelerler. Python'da embeddings'i hızlı bir şekilde hesaplamak için Sentence Transformers Python paketi (Hugging Face'in transformer paketinde de mevcuttur) kullanabilirsiniz. Bu araç, kullanıcı dostu bir arayüz sağlar ve embedding işlemini basit ve verimli hale getirir.

Aşağıdaki kod parçasında, SentenceTransformer'dan bir model nasıl yüklendiğini, üç cümle için embeddings'in nasıl hesaplandığını ve sonunda aralarındaki kosinüs benzerliğinin (cosine similarity) nasıl hesaplandığını görebilirsiniz. Bir cümlenin kendisiyle olan benzerliği her zaman 1'dir. Ayrıca, ilk ve ikinci cümleler arasında benzerlik yaklaşık 0'dır, çünkü cümlelerin ortak bir yanı yoktur. Buna karşılık, ilk ve üçüncü cümle arasındaki değer daha yüksektir çünkü bazı örtüşen bağlamlar vardır:

```python
from sentence_transformers import SentenceTransformer
model = SentenceTransformer("all-MiniLM-L6-v2")
sentences = ["The dog sits outside waiting for a treat.", "I am going swimming.", "The dog is swimming."]
embeddings = model.encode(sentences)
print(embeddings.shape)  # Output: [3, 384]
similarities = model.similarity(embeddings, embeddings)
print(similarities)  
# Output: 
# tensor([[ 1.0000, -0.0389, 0.2692],
#         [-0.0389, 1.0000, 0.3837],
#         [ 0.2692, 0.3837, 1.0000]])
#
# similarities[0, 0] = Birinci cümlenin kendisiyle olan benzerliği.
# similarities[0, 1] = Birinci ve ikinci cümle arasındaki benzerlik.
# similarities[2, 1] = Üçüncü ve ikinci cümle arasındaki benzerlik.
```

Yukarıdaki kod parçasının kaynak kodu https://github.com/PacktPublishing/LLM-Engineering/blob/main/code_snippets/08_text_embeddings.py adresinde bulunabilir. Embeddings bölümündeki örnekler, kitabın genelinde kullanılan sanal ortam içinde çalıştırılabilir, çünkü gerekli tüm bağımlılıkları içerir.

En iyi performans gösteren embedding modeli zamanla ve özel kullanım durumunuza göre değişebilir. Hugging Face'deki Massive Text Embedding Benchmark (MTEB) üzerinde belirli modelleri bulabilirsiniz. İhtiyaçlarınıza bağlı olarak, en iyi performans gösteren modeli, en iyi doğruluğa sahip olanı veya en küçük bellek ayak izine sahip olanı düşünebilirsiniz. Bu karar tamamen gereksinimlerinize (örneğin, doğruluk ve donanım) dayanır. Ancak, Hugging Face ve SentenceTransformer, farklı modeller arasında geçiş yapmayı kolaylaştırır. Böylece, her zaman çeşitli seçenekleri deneyebilirsiniz.

Görüntülerle çalışırken, bunları convolutional neural networks (CNNs) kullanarak gömebilirsiniz. Popüler CNN ağları ResNet mimarisine dayanmaktadır. Ancak, görüntü embedding tekniklerini doğrudan ses kayıtları için kullanamayız. Bunun yerine, sesin görsel bir temsilini, örneğin bir spektrogramı oluşturabilir ve ardından bu görsellere görüntü embedding modelleri uygulayabiliriz. Bu, görüntülerin ve seslerin özünü bilgisayarların anlayabileceği bir şekilde yakalamamızı sağlar.

CLIP gibi modellerden yararlanarak, pratik olarak bir metin parçasını ve bir görüntüyü aynı vektör uzayına gömebilirsiniz. Bu, bir cümleyi girdi olarak kullanarak benzer görüntüleri bulmanızı veya tam tersini yapmanızı sağlar ve CLIP'in pratikliğini gösterir. Aşağıdaki kod parçasında, CLIP'i kullanarak çılgın bir kedi görüntüsünü ve üç cümleyi kodlarız. Sonunda, resim ve cümleler arasındaki benzerliği hesaplamak için kosinüs benzerliğini kullanırız:

```python
from io import BytesIO
import requests
from PIL import Image
from sentence_transformers import SentenceTransformer

response = requests.get("https://github.com/PacktPublishing/LLM-Engineering/blob/main/images/crazy_cat.jpg?raw=true")
image = Image.open(BytesIO(response.content))
model = SentenceTransformer("clip-ViT-B-32")
img_emb = model.encode(image)
text_emb = model.encode(
    ["A crazy cat smiling.", "A white and brown cat with a yellow bandana.", "A man eating in the garden."]
)
print(text_emb.shape)  # noqa
# Output: (3, 512)
similarity_scores = model.similarity(img_emb, text_emb)
print(similarity_scores)  # noqa
# Output: tensor([[0.3068, 0.3300, 0.1719]])
```

Yukarıdaki kod parçasının kaynak kodu https://github.com/PacktPublishing/LLM-Engineering/blob/main/code_snippets/08_text_image_embeddings.py adresinde bulunabilir.

Burada, embeddings'in nasıl hesaplanabileceğine dair küçük bir giriş sağladık. Özel uygulamaların alanı geniştir, ancak bilmeniz gereken önemli şey, embeddings'in kelimeler, cümleler, belgeler, görüntüler, videolar ve grafikler gibi çoğu dijital veri kategorisi için hesaplanabileceğidir. İki farklı veri kategorisi arasındaki mesafeyi (örneğin, bir cümlenin vektörü ile bir görüntünün vektörü arasındaki mesafe) hesaplamanız gerektiğinde uzmanlaşmış modeller kullanmanız gerektiğini kavramak çok önemlidir. Bu modeller, her iki veri türünü de aynı vektör uzayına (CLIP gibi) projelerler ve böylece doğru mesafe hesaplamasını sağlar.

### Kod Açıklamaları

#### İlk Kod Parçası

1. `from sentence_transformers import SentenceTransformer`: SentenceTransformer kütüphanesini içe aktarır. Bu kütüphane, cümleleri vektör uzayına gömmek için kullanılır.
2. `model = SentenceTransformer("all-MiniLM-L6-v2")`: "all-MiniLM-L6-v2" adlı önceden eğitilmiş modeli yükler. Bu model, cümleleri embedding'lemek için kullanılır.
3. `sentences = ["The dog sits outside waiting for a treat.", "I am going swimming.", "The dog is swimming."]`: Embedding'lemek istediğimiz cümleleri içeren bir liste tanımlar.
4. `embeddings = model.encode(sentences)`: Tanımlanan cümleleri embedding'ler ve `embeddings` değişkenine atar.
5. `print(embeddings.shape)`: Embedding'lerin şeklini (shape) yazdırır. Bu, embedding'lerin boyutunu gösterir.
6. `similarities = model.similarity(embeddings, embeddings)`: Embedding'ler arasındaki benzerliği hesaplar.
7. `print(similarities)`: Hesaplanan benzerlikleri yazdırır.

#### İkinci Kod Parçası

1. `from io import BytesIO`: BytesIO sınıfını içe aktarır. Bu sınıf, byte verilerini bir dosya gibi işlemek için kullanılır.
2. `import requests`: requests kütüphanesini içe aktarır. Bu kütüphane, HTTP istekleri yapmak için kullanılır.
3. `from PIL import Image`: PIL kütüphanesinden Image sınıfını içe aktarır. Bu sınıf, görüntüleri işlemek için kullanılır.
4. `response = requests.get("https://github.com/PacktPublishing/LLM-Engineering/blob/main/images/crazy_cat.jpg?raw=true")`: Belirtilen URL'den görüntüyü indirir.
5. `image = Image.open(BytesIO(response.content))`: İndirilen görüntüyü açar.
6. `model = SentenceTransformer("clip-ViT-B-32")`: "clip-ViT-B-32" adlı önceden eğitilmiş modeli yükler. Bu model, görüntü ve metinleri embedding'lemek için kullanılır.
7. `img_emb = model.encode(image)`: Görüntüyü embedding'ler.
8. `text_emb = model.encode([...])`: Metinleri embedding'ler.
9. `similarity_scores = model.similarity(img_emb, text_emb)`: Görüntü embedding'i ile metin embedding'leri arasındaki benzerliği hesaplar.
10. `print(similarity_scores)`: Hesaplanan benzerlikleri yazdırır.

---

