# Erişim Adımının Optimizasyonu (Optimization of Retrieval Step)

Erişim adımını iki temel yolla optimize edebilirsiniz: RAG ingestion pipeline (RAG besleme hattı) içinde kullanılan embedding modellerini (embedding models) chunklara ayrılmış dokümanları kodlamak (encode) için geliştirmek ve çıkarım zamanında (inference time) kullanıcının girdisini dönüştürmek. DB'nin filtre ve arama özelliklerinden yararlanmak. Bu adım, yalnızca çıkarım zamanında, kullanıcı girdisine göre en benzer chunkları (chunks) almak için kullanılır. Her iki strateji de nihai amaca yöneliktir: sorgu (query) ve indekslenmiş veri arasındaki anlamsal benzerliği (semantic similarity) kullanarak vektör arama adımını geliştirmek.

Embedding modellerini geliştirdiğinizde, genellikle önceden eğitilmiş embedding modellerini (pre-trained embedding models) belirli jargon ve nüanslara (nuances) uyacak şekilde ince ayar (fine-tune) yapmanız gerekir, özellikle de gelişen terminoloji veya nadir terimlerin olduğu alanlar için. Embedding modelini ince ayar yapmak yerine, embedding oluşturma sürecini bir talimat/prompt ile yönlendirmek için instructor modellerden (https://huggingface.co/hkunlp/instructor-xl) yararlanabilirsiniz. Bu tür bir modeli kullanarak embedding ağınızı verilerinize uyarlamak iyi bir seçenek olabilir, çünkü bir modeli ince ayar yapmak daha fazla hesaplama ve insan kaynağı tüketir.

Aşağıdaki kod parçasında, AI hakkındaki makale başlıklarını gömen (embed) bir Instructor modeli örneği görebilirsiniz:
```python
from InstructorEmbedding import INSTRUCTOR
model = INSTRUCTOR("hkunlp/instructor-base")
sentence = "RAG Fundamentals First"
instruction = "Represent the title of an article about AI:"
embeddings = model.encode([[instruction, sentence]])
print(embeddings.shape)  # noqa
# Output: (1, 768)
```
Kaynak kodu https://github.com/PacktPublishing/LLM-Engineering/blob/main/code_snippets/08_instructor_embeddings.py adresinde bulunabilir. Instructor kodunu çalıştırmak için, farklı bir sanal ortam (virtual environment) oluşturmanız ve etkinleştirmeniz gerekir:
```bash
python3 -m venv instructor_venv && source instructor_venv/bin/activate
```
Ve gerekli Python bağımlılıklarını (dependencies) yükleyin:
```bash
pip install sentence-transformers==2.2.2 InstructorEmbedding==1.0.1
```

Spektrumun diğer tarafında, klasik filtre ve arama DB özelliklerinden yararlanarak erişiminizi nasıl geliştirebileceğiniz aşağıda açıklanmıştır:
*   **Hibrit Arama (Hybrid Search)**: Bu, vektör ve anahtar kelime tabanlı arama (keyword-based search) karışımıdır. Anahtar kelime tabanlı arama, belirli anahtar kelimeleri içeren dokümanları tanımlamada üstündür. Göreviniz kesin doğruluk talep ettiğinde ve alınan bilginin tam anahtar kelime eşleşmelerini içermesi gerektiğinde, hibrit arama öne çıkar. Vektör arama, güçlü olmasına rağmen, bazen tam eşleşmeleri bulmakta zorlanabilir, ancak daha genel anlamsal benzerlikleri bulmada üstündür. İki yöntemi birleştirerek hem anahtar kelime eşleştirmesinden hem de anlamsal benzerliklerden yararlanırsınız. İki yöntem arasındaki ağırlığı kontrol eden bir parametre, genellikle alpha olarak adlandırılır. Algoritma, daha sonra normalize edilen ve birleştirilen iki bağımsız arama yapar.
*   **Filtrelenmiş Vektör Arama (Filtered Vector Search)**: Bu tür arama, meta veri indeksini (metadata index) kullanarak meta veriler içinde belirli anahtar kelimeler için filtreleme yapar. Hibrit aramadan farkı, vektör indeksini kullanarak verileri bir kez almanız ve arama alanınızı daraltmak için vektör aramasından önce veya sonra filtreleme adımını gerçekleştirmenizdir. Uygulamada, erişim tarafında genellikle filtrelenmiş vektör arama veya hibrit arama ile başlarsınız, çünkü bunlar uygulanması oldukça hızlıdır. Bu yaklaşım, performansa göre stratejinizi ayarlamada size esneklik sağlar. Sonuçlar beklendiği gibi değilse, her zaman embedding modelinizi ince ayar yapabilirsiniz.

### Kod Açıklaması

1.  `from InstructorEmbedding import INSTRUCTOR`: Bu satır, `InstructorEmbedding` modülünden `INSTRUCTOR` sınıfını içe aktarır (import).
2.  `model = INSTRUCTOR("hkunlp/instructor-base")`: Bu satır, `INSTRUCTOR` sınıfını kullanarak bir model örneği oluşturur ve modeli "hkunlp/instructor-base" ile başlatır.
3.  `sentence = "RAG Fundamentals First"`: Bu satır, gömülecek (embed) cümleyi tanımlar.
4.  `instruction = "Represent the title of an article about AI:"`: Bu satır, gömme işlemini yönlendirmek için kullanılan talimatı tanımlar.
5.  `embeddings = model.encode([[instruction, sentence]])`: Bu satır, talimat ve cümleyi bir liste içinde modele geçirerek gömme işlemini gerçekleştirir.
6.  `print(embeddings.shape)`: Bu satır, elde edilen gömme vektörünün (embedding vector) şeklini (shape) yazdırır.

Bu kod, AI hakkındaki makale başlıklarını gömmek için bir Instructor modeli kullanır ve elde edilen gömme vektörünün şeklini yazdırır.

### İngilizce Teknik Terimlerin Türkçe Karşılıkları

*   embedding models: gömme modelleri
*   inference time: çıkarım zamanı
*   chunked documents: chunklara ayrılmış dokümanlar
*   semantic similarity: anlamsal benzerlik
*   pre-trained embedding models: önceden eğitilmiş gömme modelleri
*   fine-tune: ince ayar yapmak
*   instructor models: instructor modelleri
*   virtual environment: sanal ortam
*   dependencies: bağımlılıklar
*   hybrid search: hibrit arama
*   keyword-based search: anahtar kelime tabanlı arama
*   vector search: vektör arama
*   filtered vector search: filtrelenmiş vektör arama
*   metadata index: meta veri indeksi

Tüm kodlar ve açıklamalar yukarıda verilmiştir.

---

