# Post-Retrieval Optimizations (Post-Retrieval Optimizations)
Alma sonrası optimizasyonlar yalnızca alınan veriler üzerinde gerçekleştirilir, böylece LLM'nin (Large Language Model) performansı sınırlı bağlam pencereleri (context windows) veya gürültülü veri (noisy data) gibi sorunlar tarafından tehlikeye girmez. Bunun nedeni, alınan bağlamın (retrieved context) bazen çok büyük olabilmesi veya alakasız bilgiler içermesidir, ki bu her ikisi de LLM'yi dağıtabilir (distract). Alma sonrası adımında gerçekleştirilen iki popüler yöntem şunlardır: 
Prompt compression (Prompt Sıkıştırma): Verilerin özünü korurken gereksiz ayrıntıları ortadan kaldırın.
Re-ranking (Yeniden Sıralama): Kullanıcının girdisi (user's input) ile alınan her bir parça (retrieved chunk) arasında bir eşleme puanı (matching score) vermek için bir çapraz kodlayıcı (cross-encoder) makine öğrenimi modelini kullanın. Alınan öğeler bu puana göre sıralanır. Sadece en üstteki N sonuç (top N results) en alakalı olarak tutulur.

```python
# Yukarıdaki kod örneği yok, ancak re-ranking algoritmasının basit bir örneği aşağıda verilmiştir.
def re_ranking(user_input, retrieved_chunks, cross_encoder_model, top_n):
    # Kullanıcı girdisi ve alınan parçalar arasında eşleme puanı hesaplayın
    scores = []
    for chunk in retrieved_chunks:
        score = cross_encoder_model(user_input, chunk)
        scores.append(score)
    
    # Puanlara göre alınan parçaları sıralayın
    sorted_chunks = [chunk for _, chunk in sorted(zip(scores, retrieved_chunks), reverse=True)]
    
    # En üstteki N parçayı döndürün
    return sorted_chunks[:top_n]
```

- `re_ranking` fonksiyonu, kullanıcı girdisi, alınan parçalar, çapraz kodlayıcı modeli ve döndürülecek sonuç sayısını (`top_n`) alır.
- `cross_encoder_model`, kullanıcı girdisi ve her bir alınan parça arasında bir eşleme puanı hesaplar.
- Puanlar, alınan parçaları sıralamak için kullanılır.
- En yüksek puanlı parçalar (`top_n`) döndürülür.

# Şekil 4.7: Bi-encoder (Bi-encoder) versus cross-encoder (Çapraz Kodlayıcı)
Görülebileceği gibi, yeniden sıralama modeli (re-ranking model), basit bir benzerlik araması (similarity search) ile karşılaştırıldığında, kullanıcı girdisi ile bazı içerikler arasında daha karmaşık ilişkiler bulabilir. Ancak, bu modeli ilk alma adımında uygulayamayız çünkü maliyetlidir. Bu nedenle popüler bir strateji, embeddings (gömme vektörleri) arasındaki benzerlik mesafesini (similarity distance) kullanarak verileri almak ve alınan bilgileri bir yeniden sıralama modeli kullanarak iyileştirmektir, Şekil 4.8'de gösterildiği gibi.

```python
# Yukarıdaki kod örneği yok, ancak benzerlik mesafesi kullanarak veri alma örneği aşağıda verilmiştir.
import numpy as np

def similarity_search(query_embedding, document_embeddings):
    # Sorgu gömme vektörü ile belge gömme vektörleri arasındaki benzerlik mesafesini hesaplayın
    similarities = np.dot(document_embeddings, query_embedding) / (np.linalg.norm(document_embeddings, axis=1) * np.linalg.norm(query_embedding))
    
    # En benzer belgeleri döndürün
    return np.argsort(-similarities)

# Örnek kullanım
query_embedding = np.random.rand(128)  # Sorgu gömme vektörü
document_embeddings = np.random.rand(100, 128)  # Belge gömme vektörleri

similarities = similarity_search(query_embedding, document_embeddings)
print(similarities)
```

- `similarity_search` fonksiyonu, sorgu gömme vektörü ve belge gömme vektörleri arasındaki benzerlik mesafesini hesaplar.
- Kosinüs benzerliği (cosine similarity) kullanır.
- En benzer belgelerin indekslerini döndürür.

Yukarıda bahsedilen teknikler, potansiyel çözümlerin kapsamlı bir listesi olmaktan uzaktır. Bunları, RAG iş akışınızın (RAG workflow) her adımında neyin optimize edileceğine dair bir sezgi edinmek için örnek olarak kullandık. Gerçek şu ki, bu teknikler, çalıştığınız veri türüne göre büyük ölçüde değişebilir. Örneğin, metin ve görüntüler gibi çok modlu (multi-modal) verilerle çalışıyorsanız, önceki tekniklerin çoğu, yalnızca metin için tasarlandıkları için çalışmaz.

# Şekil 4.8: The re-ranking algorithm (Yeniden Sıralama Algoritması)
Özetle, bu optimizasyonların temel amacı, RAG algoritmasını üç ana aşamada geliştirmektir: alma öncesi (pre-retrieval), alma (retrieval) ve alma sonrası (post-retrieval). Bu, geliştirilmiş vektör indeksleme (vector indexing) için verilerin ön işlenmesi (preprocessing), daha doğru aramalar için kullanıcı sorgularını ayarlama, gömme modelini geliştirme, klasik filtreleme veritabanı işlemlerini kullanma ve gürültülü verileri kaldırma gibi işlemleri içerir. Bu hedefleri aklınızda tutarak, RAG iş akışınızı veri işleme ve alma için etkili bir şekilde optimize edebilirsiniz.

---

