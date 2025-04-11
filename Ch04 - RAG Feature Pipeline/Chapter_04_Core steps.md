# Çoğu RAG Özellik Ardışık Süreçleri (RAG Feature Pipelines)
Çoğu RAG özellik ardışık süreçleri (RAG feature pipelines) beş temel adımdan oluşur. LLM Twin mimarisinde uygulanan da bir istisna değildir. Böylece, bu modeli diğer RAG uygulamaları için hızlı bir şekilde uyarlayabilirsiniz, ancak LLM Twin'in RAG özellik ardışık süreci (RAG feature pipeline) şuna benzer:

## Veri Çıkarımı (Data Extraction)
MongoDB veri ambarından (data warehouse) en son makaleleri, kod depolarını (code repositories) ve gönderileri (posts) çıkarın. Çıkarma adımında (extraction step), genellikle işleme için ihtiyaç duyduğunuz tüm verileri toplarsınız.

## Temizleme (Cleaning)
Veri ambarından alınan veriler standartlaştırılmış ve kısmen temizdir, ancak metnin yalnızca yararlı bilgiler içermesini, yinelenmemesini (duplicated) ve embedding modeli (embedding model) tarafından yorumlanabilmesini sağlamalıyız. Örneğin, metni embedding modeline geçirmeden önce tüm ASCII olmayan karakterleri (non-ASCII characters) temizlemeli ve normalleştirmeliyiz. Ayrıca, bilgileri anlamsal olarak yoğun tutmak için (semantically dense), tüm URL'leri yer tutucularla (placeholders) değiştirmeye ve tüm emojileri kaldırmaya karar verdik. Temizleme adımı (cleaning step) bilimden çok sanattır. Bu nedenle, bir değerlendirme mekanizması (evaluation mechanism) ile ilk yinelemeyi (iteration) yaptıktan sonra, muhtemelen bunu yineleyecek ve geliştireceksiniz.

## Parçalama (Chunking)
Her veri kategorisine (data category) ve embedding modeline göre çeşitli parçalama stratejileri (chunking strategies) benimsemelisiniz. Örneğin, kod depolarıyla (code repositories) çalışırken, parçaların daha geniş olmasını isterken, makalelerle çalışırken daha dar veya paragraf düzeyinde olmasını istersiniz. Verilerinize bağlı olarak, belgenizi bölüm (chapter), bölüm (section), paragraf (paragraph), cümle (sentence) veya sadece sabit bir pencere boyutuna (fixed window size) göre bölmeniz gerekip gerekmediğine karar vermelisiniz. Ayrıca, parça boyutunun (chunk size) embedding modelinin maksimum giriş boyutunu (maximum input size) aşmadığından emin olmalısınız. Bu nedenle, genellikle bir belgeyi veri yapınıza (data structure) ve modelin maksimum giriş boyutuna göre parçalarsınız.

## Embedding
Her parçayı (chunk) bireysel olarak seçtiğiniz bir embedding modeline geçirirsiniz. Uygulama açısından, bu adım genellikle en basit olanıdır, çünkü SentenceTransformer ve Hugging Face gibi araçlar çoğu embedding modeli için yüksek düzeyli arayüzler (high-level interfaces) sağlar. Bu bölümün "Gömme nedir?" (What are embeddings?) kısmında açıklanan en kritik kararlar, hangi modelin kullanılacağına ve ince ayar yapılıp yapılmayacağına karar vermektir. Örneğin, SentenceTransformer'dan nispeten küçük olan ve çoğu makinede çalışan "all-mpnet-base-v2" embedding modelini kullandık. Ancak, bu kitabı okurken state of the art'a göre daha güçlü bir şeyle embedding modelini hızlı bir şekilde yapılandırabileceğiniz bir yapılandırma dosyası sağlıyoruz. Hugging Face'deki MTEB'de (https://huggingface.co/spaces/mteb/leaderboard) diğer seçenekleri hızlı bir şekilde bulabilirsiniz.

## Veri Yükleme (Data Loading)
Son adım, parçalanmış bir belgenin (chunked document) embeddingini ve yazar, belge kimliği (document ID), içerik, URL, platform ve oluşturma tarihi gibi meta verilerini (metadata) birleştirir. Sonunda, vektörü (vector) ve meta verileri Qdrant ile uyumlu bir yapıya sarar ve vektör veritabanına (vector DB) iteriz. Özellikler için tek kaynak olarak Qdrant'ı kullanmak istediğimiz için, temizlenmiş belgeleri de (parçalanmadan önce) Qdrant'a iteriz. Vektörler olmadan veri gönderebiliriz, çünkü Qdrant'ın meta veri indeksi (metadata index) NoSQL veritabanı (NoSQL DB) gibi davranır. Bu nedenle, bir vektör eklemeden meta verileri göndermek, standart bir NoSQL motoru kullanmak gibidir.

Kod açıklamaları:

Yukarıdaki metinde kod bulunmamaktadır. Ancak, embedding modelini yapılandırmak için kullanılan bir yapılandırma dosyasından bahsedilmektedir. Aşağıda örnek bir kod yapısı verilmiştir:
```python
# Yapılandırma dosyası örneği
import yaml

# Yapılandırma dosyasını yükle
with open('config.yaml', 'r') as f:
    config = yaml.safe_load(f)

# Embedding modelini yapılandır
embedding_model = config['embedding_model']
```
Bu kod, `config.yaml` adlı bir yapılandırma dosyasını yükler ve embedding modelini yapılandırır.

Ayrıca, embedding modelini kullanmak için aşağıdaki gibi bir kod örneği verilebilir:
```python
# Embedding modeli örneği
from sentence_transformers import SentenceTransformer

# Embedding modelini yükle
model = SentenceTransformer('all-mpnet-base-v2')

# Metni embedding modeline geçir
text = "Örnek metin"
embedding = model.encode(text)

# Embedding'i yazdır
print(embedding)
```
Bu kod, `all-mpnet-base-v2` adlı embedding modelini yükler ve bir metni embedding modeline geçirerek embedding'i elde eder.

---

