#Kullanıcı Girdisinin İşlenmesi ve Vektör Veritabanında Sorgulama

Alma bileşenleri (retrieval components), kullanıcının girdisini (metin, resim, ses, vb.) alır, bunu gömülü vektöre (embed) dönüştürür ve vektör veritabanında (vector DB) kullanıcının girdisine benzer vektörleri sorgular. Alma adımının (retrieval step) birincil işlevi, kullanıcının girdisini vektör veritabanında dizin (index) olarak kullanılan gömülü vektörlerle aynı vektör uzayına (vector space) yansıtmak (project). Bu, vektör depolamadan (vector storage) alınan gömülü vektörleri kullanıcının girdi vektörle karşılaştırarak en üstteki K'nın en benzer girişlerini (most similar entries) bulmamızı sağlar. Bu girişler daha sonra, Büyük Dil Modeli'ne (LLM) cevabı oluşturmak üzere iletilen istemi (prompt) zenginleştirmek (augment) için içerik olarak hizmet eder.

İki vektör arasında karşılaştırma yapmak için Öklid (Euclidean) veya Manhattan mesafesi gibi bir mesafe metriği (distance metric) kullanmalısınız. Ancak en popüler olanı, iki vektör arasındaki açının kosinüsünün 1 eksiğine eşit olan kosinüs mesafesidir (cosine distance). Aşağıdaki gibi hesaplanır: 
-1 ile 1 arasında değişir, vektörler A ve B zıt yönlerde olduğunda -1 değerini, dik olduklarında (orthogonal) 0 değerini ve aynı yönde olduklarında 1 değerini alır. Çoğu zaman, kosinüs mesafesi doğrusal olmayan (non-linear) karmaşık vektör uzaylarında iyi çalışır. Ancak, iki vektör arasındaki uygun mesafeyi seçmenin veri ve kullanılan gömme modeline (embedding model) bağlı olduğu unutulmamalıdır.

Dikkat edilmesi gereken kritik bir faktör, kullanıcının girdisi ve gömülü vektörlerin aynı vektör uzayında olması gerektiğidir. Aksi takdirde, aralarındaki mesafeyi hesaplayamazsınız. Bunu yapmak için, kullanıcının girdisini, RAG alım hattında (ingestion pipeline) ham belgeleri işlediğiniz şekilde ön işlemden (preprocess) geçirmek önemlidir. Bu, aynı fonksiyonları, modelleri ve hiperparametreleri kullanarak kullanıcının girdisini temizlemek, parçalamak (chunk) (gerekirse) ve gömmek anlamına gelir. Bu, eğitim ve çıkarım (inference) arasında verileri aynı şekilde özelliklere (features) ön işlemden geçirmek zorunda olmaya benzer; aksi takdirde, çıkarım yanlış sonuçlar verecektir - eğitim-servis eğriliği (training-serving skew) olarak da bilinen bir fenomen.

### Kodların Yazılması ve Açıklanması

İlgili kodlar aşağıda verilmiştir, ancak orijinal metinde kod pasajları bulunmamaktadır. Bununla birlikte, kosinüs mesafesinin hesaplanması için örnek bir kod pasajı aşağıda verilmiştir:

```python
import numpy as np

def cosine_distance(vector_a, vector_b):
    # İki vektörün kosinüs benzerliğini hesapla
    cosine_similarity = np.dot(vector_a, vector_b) / (np.linalg.norm(vector_a) * np.linalg.norm(vector_b))
    # Kosinüs mesafeyi hesapla
    distance = 1 - cosine_similarity
    return distance

# Örnek vektörler
vector_a = np.array([1, 2, 3])
vector_b = np.array([4, 5, 6])

# Kosinüs mesafeyi hesapla
distance = cosine_distance(vector_a, vector_b)
print("Kosinüs Mesafesi:", distance)
```

### Kodun Satır Satır Açıklanması

1. `import numpy as np`: Numpy kütüphanesini `np` takma adıyla içe aktarır. Numpy, sayısal işlemler için kullanılan güçlü bir kütüphanedir.

2. `def cosine_distance(vector_a, vector_b):`: `cosine_distance` adlı bir fonksiyon tanımlar. Bu fonksiyon, iki vektör arasındaki kosinüs mesafeyi hesaplar.

3. `cosine_similarity = np.dot(vector_a, vector_b) / (np.linalg.norm(vector_a) * np.linalg.norm(vector_b))`: İki vektör arasındaki kosinüs benzerliğini hesaplar. `np.dot` fonksiyonu iki vektörün nokta çarpımını (dot product) hesaplar. `np.linalg.norm` fonksiyonu ise bir vektörün büyüklüğünü (norm) hesaplar.

4. `distance = 1 - cosine_similarity`: Kosinüs mesafeyi, kosinüs benzerliğinden 1 çıkararak hesaplar.

5. `return distance`: Hesaplanan kosinüs mesafeyi döndürür.

6. `vector_a = np.array([1, 2, 3])` ve `vector_b = np.array([4, 5, 6])`: Örnek iki vektör tanımlar.

7. `distance = cosine_distance(vector_a, vector_b)`: Tanımlanan `cosine_distance` fonksiyonunu kullanarak iki örnek vektör arasındaki kosinüs mesafeyi hesaplar.

8. `print("Kosinüs Mesafesi:", distance)`: Hesaplanan kosinüs mesafeyi yazdırır.

#Kullanıcı Girdisinin İşlenmesi ve Vektör Veritabanında Sorgulama (The retrieval components take the user’s input...)

Alma bileşenleri (retrieval components), kullanıcının girdisini (text, image, audio, etc.), gömülü vektöre (embed) dönüştürür ve vektör veritabanında (vector DB) kullanıcının girdisine benzer vektörleri sorgular. Alma adımının (retrieval step) birincil işlevi, kullanıcının girdisini vektör veritabanında dizin (index) olarak kullanılan gömülü vektörlerle aynı vektör uzayına (vector space) yansıtmak (project). Bu, vektör depolamadan (vector storage) alınan gömülü vektörleri kullanıcının girdi vektörle karşılaştırarak en üstteki K'nın en benzer girişlerini (most similar entries) bulmamızı sağlar. Bu girişler daha sonra, Büyük Dil Modeli'ne (LLM) cevabı oluşturmak üzere iletilen istemi (prompt) zenginleştirmek (augment) için içerik olarak hizmet eder.

İki vektör arasında karşılaştırma yapmak için Öklid (Euclidean) veya Manhattan mesafesi gibi bir mesafe metriği (distance metric) kullanmalısınız. Ancak en popüler olanı, iki vektör arasındaki açının kosinüsünün 1 eksiğine eşit olan kosinüs mesafesidir (cosine distance). 
-1 ile 1 arasında değişir, vektörler A ve B zıt yönlerde olduğunda -1 değerini, dik olduklarında (orthogonal) 0 değerini ve aynı yönde olduklarında 1 değerini alır. Çoğu zaman, kosinüs mesafesi doğrusal olmayan (non-linear) karmaşık vektör uzaylarında iyi çalışır. Ancak, iki vektör arasındaki uygun mesafeyi seçmenin veri ve kullanılan gömme modeline (embedding model) bağlı olduğu unutulmamalıdır.

Dikkat edilmesi gereken kritik bir faktör, kullanıcının girdisi ve gömülü vektörlerin aynı vektör uzayında olması gerektiğidir. Aksi takdirde, aralarındaki mesafeyi hesaplayamazsınız. Bunu yapmak için, kullanıcının girdisini, RAG alım hattında (ingestion pipeline) ham belgeleri işlediğiniz şekilde ön işlemden (preprocess) geçirmek önemlidir. Bu, aynı fonksiyonları, modelleri ve hiperparametreleri kullanarak kullanıcının girdisini temizlemek, parçalamak (chunk) (gerekirse) ve gömmek anlamına gelir. Bu, eğitim ve çıkarım (inference) arasında verileri aynı şekilde özelliklere (features) ön işlemden geçirmek zorunda olmaya benzer; aksi takdirde, çıkarım yanlış sonuçlar verecektir - eğitim-servis eğriliği (training-serving skew) olarak da bilinen bir fenomen.

The retrieval components take the user’s input (text, image, audio, etc.), embed it, and query the vector DB for similar vectors to the user’s input. The primary function of the retrieval step is to project the user’s input into the same vector space as the embeddings used as an index in the vector DB. This allows us to find the top K’s most similar entries by comparing the embeddings from the vector storage with the user’s input vector. These entries then serve as content to augment the prompt that is passed to the LLM to generate the answer. You must use a distance metric to compare two vectors, such as the Euclidean or Manhattan distance. But the most popular one is the cosine distance, which is equal to 1 minus the cosine of the angle between two vectors, as follows: It ranges from -1 to 1 , with a value of -1 when vectors A and B are in opposite directions, 0 if they are orthogonal, and 1 if they point in the same direction. Most of the time, the cosine distance works well in non-linear complex vector spaces. However, it is essential to notice that choosing the proper distance between two vectors depends on your data and the embedding model you use. One critical factor to highlight is that the user’s input and embeddings must be in the same vector space. Otherwise, you cannot compute the distance between them. To do so, it is essential to preprocess the user input in the same way you processed the raw documents in the RAG ingestion pipeline. This means you must clean, chunk (if necessary), and embed the user’s input using the same functions, models, and hyperparameters. This is similar to how you have to preprocess the data into features in the same way between training and inference; otherwise, the inference will yield inaccurate results—a phenomenon also known as the training-serving skew.

---

