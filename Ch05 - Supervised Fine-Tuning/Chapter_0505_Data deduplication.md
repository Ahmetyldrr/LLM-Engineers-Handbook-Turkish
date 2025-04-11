# Veri Kümesi Çeşitliliği (Dataset Diversity)

Veri kümesi çeşitliliği, yeni ve görülmemiş verilere iyi bir şekilde genellenebilen modellerin eğitilmesi için temeldir. Bir veri kümesi yinelenen (duplicate) veya neredeyse yinelenen (near-duplicate) örnekler içerdiğinde, birkaç soruna yol açabilir: 
Aşırı uyum (Overfitting): Modeller genel desenleri öğrenmek yerine belirli örnekleri ezberleyebilir.
Yanlı performans (Biased performance): Aşırı temsil edilen veri noktaları, modelin performansını belirli girdi türlerine doğru kaydırabilir.
Etkisiz eğitim (Inefficient training): Kayıtlı veri, eğitim süresini artırabilir ve ek değerli bilgi sağlamayabilir.
Suni değerlendirme ölçütleri (Inflated evaluation metrics): Test kümelerindeki yinelenen veriler, aşırı iyimser performans tahminlerine yol açabilir.

Veri kümelerini yinelenen örneklerden arındırmak için, tam (exact) ve bulanık (fuzzy) yinelenen örnekleri birbirinden ayırırız.

## Tam Yinelenen Örneklerin Çıkarılması (Exact Deduplication)

Tam yinelenen örneklerin çıkarılması, veri normalizasyonu (data normalization), hash oluşturma (hash generation) ve yinelenen örneklerin kaldırılması (duplicate removal) gibi basit bir süreç yoluyla özdeş örnekleri kaldırır.

```python
# Veri normalizasyonu
veri = veri.lower()  # Metni küçük harfe çevirme

# Hash oluşturma
import hashlib
hash_object = hashlib.md5(veri.encode())
hash_degeri = hash_object.hexdigest()

# Yinelenen örneklerin kaldırılması
veri_kumesi = set()  # Benzersiz hash değerlerini saklamak için
if hash_degeri not in veri_kumesi:
    veri_kumesi.add(hash_degeri)
```

- `veri = veri.lower()`: Metni küçük harfe çevirerek veri normalizasyonu yapar.
- `hashlib.md5(veri.encode())`: Verinin MD5 hash değerini oluşturur.
- `veri_kumesi = set()`: Benzersiz hash değerlerini saklamak için bir küme oluşturur.
- `if hash_degeri not in veri_kumesi`: Hash değerinin daha önce görülüp görülmediğini kontrol eder.

## Bulanık Yinelenen Örneklerin Çıkarılması (Fuzzy Deduplication)

Bulanık yinelenen örneklerin çıkarılması için en popüler yaklaşım MinHash yöntemidir. MinHash, veri öğeleri için kompakt temsiller (signatures) oluşturur ve bu temsilleri karşılaştırarak neredeyse yinelenen örnekleri verimli bir şekilde tanımlar.

```python
# MinHash kullanarak bulanık yinelenen örneklerin çıkarılması
from datasketch import MinHash

def minhash_veri(veri, ngram_degeri=5, num_perm=128):
    minhash = MinHash(num_perm=num_perm)
    for i in range(len(veri) - ngram_degeri + 1):
        shingle = veri[i:i+ngram_degeri]
        minhash.update(shingle.encode('utf8'))
    return minhash

veri1 = "örnek metin"
veri2 = "benzer metin"

minhash1 = minhash_veri(veri1)
minhash2 = minhash_veri(veri2)

# Jaccard benzerliğini hesaplama
benzerlik = minhash1.jaccard(minhash2)
print("Jaccard benzerliği:", benzerlik)
```

- `minhash_veri` fonksiyonu, verilen veri için MinHash değeri hesaplar.
- `MinHash(num_perm=num_perm)`: Belirtilen permütasyon sayısına göre MinHash nesnesi oluşturur.
- `minhash.update(shingle.encode('utf8'))`: Shingle değerlerini MinHash nesnesine ekler.
- `minhash1.jaccard(minhash2)`: İki MinHash değeri arasındaki Jaccard benzerliğini hesaplar.

## Anlamsal Benzerlik (Semantic Similarity)

Anlamsal benzerlik, yinelenen örneklerin çıkarılması için metinlerin anlamlarına odaklanır. Bu yöntem, kelimeleri veya örnekleri vektör temsillerine dönüştürerek çeşitli doğal dil işleme tekniklerini kullanır.

```python
# Anlamsal benzerlik için vektör temsillerinin oluşturulması
from sentence_transformers import SentenceTransformer

model = SentenceTransformer('paraphrase-MiniLM-L6-v2')
veri1_vektor = model.encode("örnek metin")
veri2_vektor = model.encode("benzer metin")

# Kosinüs benzerliğini hesaplama
from sklearn.metrics.pairwise import cosine_similarity
benzerlik = cosine_similarity([veri1_vektor], [veri2_vektor])
print("Kosinüs benzerliği:", benzerlik)
```

- `SentenceTransformer`: Cümleleri vektör temsillerine dönüştürmek için kullanılan bir modeldir.
- `model.encode`: Verilen metni vektör temsiline dönüştürür.
- `cosine_similarity`: İki vektör arasındaki kosinüs benzerliğini hesaplar.

Büyük veri kümeleri için, benzer vektörleri gruplamak amacıyla K-means, DBSCAN veya hiyerarşik kümeleme gibi kümeleme teknikleri uygulanabilir. Her bir küme içinde, temsilci bir örnek tutulabilirken diğerleri yinelenen olarak işaretlenebilir.

---

