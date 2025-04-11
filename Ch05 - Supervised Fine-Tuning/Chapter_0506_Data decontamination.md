# Veri Dekontaminasyonu
Veri dekontaminasyonu (Data decontamination), eğitim veri setinin (training dataset) değerlendirme veya test setlerindekilere (evaluation or test sets) özdeş veya oldukça benzer örnekler içermemesini sağlama sürecidir. Bu adım, model değerlendirmesinin (model evaluation) kalitesini sağlamak ve aşırı uyum (overfitting) veya test verilerinin ezberlenmesini (memorization of test data) önlemek için önemlidir. Veri dekontaminasyonu, veri kopyalama (data deduplication) tekniklerini kullanır. Tam eşleme (Exact matching), değerlendirme setlerindekilere özdeş olan herhangi bir eğitim örneğini kaldırmak için kullanılabilir. Bu, hash fonksiyonları (hash functions) veya doğrudan dizgi karşılaştırmaları (direct string comparisons) kullanılarak yapılabilir. Ardından, değerlendirme örneklerine çok benzeyen eğitim örneklerini, tam olarak aynı olmasalar bile, tanımlamak ve kaldırmak için yakın kopya tespit yöntemleri (near-duplicate detection methods) de kullanabiliriz. Bu genellikle MinHash (MinHash) veya n-gram veya gömmelere (embeddings) dayalı benzerlik puanlarının (similarity scores) hesaplanması gibi teknikleri içerir. Veri dekontaminasyonunu gerçekleştirmenin basit bir yolu, veri kopyalama aşamasında (data deduplication stage) değerlendirme setinizi talimat veri setine (instruction dataset) eklemektir. Bu durumda, yalnızca talimat veri setinden örnekleri kaldırmamız gerekir; bu, farklı şekillerde uygulanabilir (yalnızca ilk kopyayı filtreleme, değerlendirme örneklerinin indekslerini kaydetme, vb.). İdeal olarak, bu süreci tam olarak otomatize etmek için veri kopyalama aşamasında değerlendirme setlerinizi otomatik olarak ekleyebilirsiniz. Bu, özellikle birkaç özel kıyaslama sürümü üzerinde yineleme yapıyorsanız etkilidir. Veri dekontaminasyonunun bir başka yönü, değerlendirme verileriyle aynı kaynaktan türetilmiş olabilecek örnekleri filtrelemektir. Bu, örtüşen ifadeleri (overlapping phrases), benzer cümle yapılarını (similar sentence structures) veya ortak meta verileri (common metadata) kontrol etmeyi içerebilir. Uygulayıcılar, değerlendirme setlerinde kullanıldığı bilinen belirli kaynaklardan verileri tanımlamak ve hariç tutmak için kaynak izleme (provenance tracking) de kullanabilirler.

**Kodlar:**
```python
# Veri kopyalama aşamasında değerlendirme setini talimat veri setine ekleme
def data_decontamination(instruction_dataset, evaluation_set):
    combined_dataset = instruction_dataset + evaluation_set
    # Hash fonksiyonları kullanarak tam eşleme
    hash_values = [hash(sample) for sample in combined_dataset]
    # Kopyaları tespit etme ve kaldırma
    unique_samples = []
    seen_hash_values = set()
    for i, hash_value in enumerate(hash_values):
        if hash_value not in seen_hash_values:
            unique_samples.append(combined_dataset[i])
            seen_hash_values.add(hash_value)
    # Yalnızca talimat veri setinden kopyaları kaldırma
    filtered_samples = [sample for sample in unique_samples if sample not in evaluation_set]
    return filtered_samples

# MinHash kullanarak yakın kopya tespiti
def minhash_near_duplicate_detection(instruction_dataset, evaluation_set):
    # MinHash nesnesi oluşturma
    minhash = MinHash(num_perm=128)
    # Değerlendirme seti için MinHash değerlerini hesaplama
    evaluation_minhash_values = []
    for sample in evaluation_set:
        minhash.update(sample.encode('utf-8'))
        evaluation_minhash_values.append(minhash.digest())
    # Talimat veri seti için MinHash değerlerini hesaplama
    instruction_minhash_values = []
    for sample in instruction_dataset:
        minhash.update(sample.encode('utf-8'))
        instruction_minhash_values.append(minhash.digest())
    # Benzerlik puanlarını hesaplama
    similarity_scores = []
    for instruction_minhash in instruction_minhash_values:
        scores = []
        for evaluation_minhash in evaluation_minhash_values:
            score = instruction_minhash.jaccard(evaluation_minhash)
            scores.append(score)
        similarity_scores.append(max(scores))
    # Yakın kopyaları tespit etme ve kaldırma
    threshold = 0.8
    filtered_samples = []
    for i, score in enumerate(similarity_scores):
        if score < threshold:
            filtered_samples.append(instruction_dataset[i])
    return filtered_samples
```

**Kod Açıklaması:**

1. `data_decontamination` fonksiyonu:
   - `instruction_dataset` ve `evaluation_set` listelerini birleştirir.
   - Hash fonksiyonları kullanarak her bir örnek için benzersiz bir değer hesaplar.
   - Kopyaları tespit etmek için bu hash değerlerini kullanır ve yalnızca benzersiz örnekleri saklar.
   - Yalnızca talimat veri setinden kopyaları kaldırarak filtrelenmiş örnekleri döndürür.

2. `minhash_near_duplicate_detection` fonksiyonu:
   - MinHash nesnesi oluşturur ve değerlendirme seti ile talimat veri seti için MinHash değerlerini hesaplar.
   - Jaccard benzerlik ölçütünü kullanarak talimat veri setindeki her bir örnek için değerlendirme setindeki örneklerle benzerlik puanlarını hesaplar.
   - Belirli bir eşik değerinin altında benzerlik puanı olan örnekleri filtreleyerek yakın kopyaları tespit eder ve kaldırır.

Her iki fonksiyon da veri dekontaminasyonu sürecinde kullanılabilir. İlk fonksiyon tam eşlemeyi, ikinci fonksiyon ise yakın kopya tespitini gerçekleştirir.

---

