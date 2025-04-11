# Veri Keşfi (Data Exploration)

Veri keşfi, uygulayıcıların eğitim verilerine (training data) aşina olmasını gerektiren sürekli bir süreçtir. Hem manuel inceleme (manual inspection) hem de otomatik analiz (automated analysis) içerir, her biri veri setinin özellikleri (characteristics), güçlü yönleri ve potansiyel eksikliklerini anlamada çok önemli bir rol oynar.

# Manuel Veri Seti Keşfi (Manual Dataset Exploration)

Manuel veri seti keşfi, zaman alıcı olsa da önemli bir adımdır. Otomatik süreçlerin gözden kaçırabileceği hataları ve tutarsızlıkları ortaya çıkarır, biçimlendirme sorunları (formatting issues), veri girişi hataları (data entry mistakes), tutarsız mantık (incoherent reasoning) ve gerçek yanlışlıkları (factual inaccuracies) dahil. Bu süreç, veri setinin içeriği ve stili hakkında nitel bilgiler sağlar. Verimliliği artırmak için araştırmacılar, tabakalı örnekleme (stratified sampling) (çeşitli örnekler seçme), sistematik inceleme (systematic review) (kriter kontrol listesi kullanma) ve işbirlikçi inceleme (collaborative review) (birden fazla inceleyiciyi dahil etme) gibi teknikleri kullanabilir.

# Şekil 5.4 – Argilla'nın İşbirlikçi Veri Kalitesi Değerlendirmesi ve Keşfi için Arayüzü

İstatistiksel analiz (statistical analysis), kelime çeşitliliği (vocabulary diversity), potansiyel önyargılar (potential biases) ve kavram temsili (concept representation) ortaya koyan tamamlayıcı bir tekniktir. Bu süreç, büyük metin hacimlerinin tokenleştirilmesi (tokenization) ve analizi için NLTK veya spaCy gibi doğal dil işleme kütüphanelerini (natural language processing libraries) kullanır. Matplotlib veya Seaborn gibi görselleştirme araçları (visualization tools), histogramlar ve kelime bulutları (word clouds) oluşturarak sezgisel desen tanıma sağlar. Bu teknikler, veri seti kompozisyonu, dil genişliği ve model çıktılarını etkileyebilecek olası kültürel veya bağlamsal tercihleri hakkında fikir sağlar.

# Konu Kümeleme (Topic Clustering)

Konu kümeleme, benzer belgeleri veya metin parçalarını otomatik olarak gruplandırarak verilerdeki temel temaları ve desenleri ortaya koyar. Bu süreç, büyük metin derlemelerinin (text corpora) içeriğini anlamak, trendleri belirlemek ve bilgiyi anlamlı bir şekilde düzenlemek için özellikle önemlidir. Genellikle veri görselleştirme (data visualization) ile ilişkilendirilir ve benzer örneklerin kümelerini gösteren şekiller içerir.

Örneğin, çeşitli programlama dilleri hakkında bir talimat veri seti oluşturma görevini düşünün. Çevrimiçi forumlardan, belgelerden ve eğitim materyallerinden programlama ile ilgili büyük bir metin derlemesi topladınız. İlk olarak, konu kümeleme, veri setinde bulunan farklı programlama dillerini (Python, JavaScript, vb.) belirlemeye yardımcı olabilir. İkinci olarak, her dil kümesi içinde, hata işleme (error handling), veri yapıları (data structures) ve web çerçeveleri (web frameworks) gibi alt konuları daha da belirleyebilirsiniz. Bu, her dil ve alt konunun derlemede dengeli bir şekilde temsil edilmesini sağlar.

# Şekil 5.5 – Nomic Atlas ile Yapılan Tarihi TikTok Veri Seti Gösterimi

Konu kümeleme gerçekleştirmek için çeşitli araçlar mevcuttur, her birinin kendi güçlü yönleri ve yaklaşımları vardır. Örneğin, Hugging Face'in metin kümeleme (text-clustering) aracı, metni vektör uzayına (vector space) gömmek için cümle dönüştürücüleri (sentence transformers), boyut azaltma (dimensionality reduction) için UMAP ve kümeleme (clustering) için DBSCAN kullanan basit bir işlem hattı sağlar. Ayrıca, kümeleri otomatik olarak etiketlemek için bir LLM kullanır ve görselleştirmeler çıktırabilir. Nomic Atlas (Şekil 5.5), BunkaTopics ve Lilac, ek özelliklerle benzer yaklaşımlar öneren alternatiflerdir.

**Kod Açıklaması**

Yukarıdaki metinde kod bulunmamaktadır. Ancak, konu kümeleme için kullanılan bazı araçların kod örnekleri aşağıda verilmiştir:

*   Hugging Face'in metin kümeleme aracı:
    ```python
from sentence_transformers import SentenceTransformer
from sklearn.cluster import DBSCAN
from sklearn.preprocessing import StandardScaler
import numpy as np

# Cümle dönüştürücü modelini yükle
model = SentenceTransformer('distilbert-base-nli-mean-tokens')

# Metinleri vektör uzayına göm
sentence_embeddings = model.encode(metinler)

# Boyut azaltma
umap_embeddings = UMAP(n_components=5, random_state=42).fit_transform(sentence_embeddings)

# Kümeleme
cluster = DBSCAN(eps=0.5, min_samples=5).fit(umap_embeddings)

# Küme etiketlerini al
labels = cluster.labels_
```
*   Nomic Atlas:
    ```python
import nomic

# Nomic Atlas'ı başlat
atlas = nomic.Atlas()

# Metinleri yükle
atlas.add_text(metinler)

# Kümelemeyi gerçekleştir
atlas.cluster()

# Küme etiketlerini al
labels = atlas.get_labels()
```
Bu kod örnekleri, konu kümeleme için kullanılan araçların nasıl kullanılabileceğini göstermektedir. Ancak, gerçek kod, kullanılan kütüphane ve araçlara bağlı olarak değişebilir.

---

