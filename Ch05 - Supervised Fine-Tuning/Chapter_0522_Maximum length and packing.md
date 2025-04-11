#Maksimum Sıra Uzunluğu (Maximum Sequence Length)

Maksimum sıra uzunluğu, modelin işleyebileceği en uzun girdiyi belirler. Genellikle 512 ile 4,096 token (token) arasında ayarlanır, ancak görev ve mevcut GPU belleğine (GPU memory) bağlı olarak 128,000 veya daha fazlasına kadar çıkabilir. Örneğin, birçok dil oluşturma görevi (language generation tasks) için 2,048 tokenlik bir maksimum uzunluk yaygındır, RAG uygulamaları (RAG applications) ise 8,192 token veya daha fazlasını kullanabilir. Girdi verilerini işlerken, bu sınırdan daha uzun diziler kısaltılır (truncated), yani fazla tokenler kaldırılır. Kısaltma, dizinin başında (sol kısaltma, left truncation) veya sonunda (sağ kısaltma, right truncation) gerçekleşebilir. Örneğin, 1,024 tokenlik bir maksimum uzunlukla, 1,500 tokenlik bir girdi 476 token kaldırılmış olur. Bu parametre, doğrudan batch boyutunu (batch size) ve bellek kullanımını (memory usage) etkiler; 1,024 maksimum uzunluklu 12 batch boyutu, 12,288 token (12 * 1,024) içerir, aynı batch boyutuyla 512 maksimum uzunlukta ise sadece 6,144 token içerir. Performansı ve kaynak kullanımını optimize etmek için bu parametreyi GPU yetenekleriniz ve eğitim verilerinizin doğası ile dengelemeniz önemlidir.

#Paketleme (Packing)

Paketleme, her eğitim batch'inin kullanımını en üst düzeye çıkarır. Her batch'e bir örnek atamak yerine, paketleme birden fazla küçük örneği tek bir batch'e birleştirir, böylece her yinelemede işlenen veri miktarını etkili bir şekilde artırır. Örneğin, maksimum sıra uzunluğunuz 1,024 token ise, ancak örneklerinizin çoğu yalnızca 200-300 token uzunluğundaysa, paketleme her batch slotuna 3-4 örnek sığdırmanıza olanak tanır. Bu yaklaşım, özellikle birçok kısa dizi içeren veri kümeleriyle (datasets) çalışırken eğitim verimliliğini önemli ölçüde artırabilir. Ancak, paketleme, model dikkatinin (model attention) paketlenmiş örnekler arasında çapraz geçmemesini sağlamak için dikkatli bir uygulama gerektirir. Bu genellikle, aynı paketlenmiş dizi içindeki farklı örneklerden tokenlere dikkat etmesini önleyen dikkat maskeleri (attention masks) kullanılarak elde edilir.

İngilizce Teknik Terimler Parantez İçinde Eklenmiştir.

Kodların Ayrıntılı Açıklaması:

Bu metinde kod bulunmamaktadır. Ancak, anlatılan konseptlerle ilgili örnek kodlar aşağıdaki gibi olabilir:

```python
# Maksimum sıra uzunluğunu belirleme
max_sequence_length = 1024

# Girdi dizisini kısaltma
def truncate_sequence(input_sequence, max_length):
    if len(input_sequence) > max_length:
        return input_sequence[:max_length]  # Sağ kısaltma
    return input_sequence

# Paketleme örneği
def pack_samples(samples, max_length):
    packed_batch = []
    current_batch = []
    current_length = 0
    
    for sample in samples:
        if current_length + len(sample) <= max_length:
            current_batch.append(sample)
            current_length += len(sample)
        else:
            packed_batch.append(current_batch)
            current_batch = [sample]
            current_length = len(sample)
    
    if current_batch:
        packed_batch.append(current_batch)
    
    return packed_batch

# Dikkat maskesi örneği
def create_attention_mask(packed_batch):
    attention_masks = []
    
    for batch in packed_batch:
        mask = []
        for sample in batch:
            mask.extend([1] * len(sample))  # Dikkat edilecek tokenler
        # Farklı örnekler arasındaki dikkatini önlemek için maskeyi genişlet
        attention_masks.append(mask)
    
    return attention_masks
```

1. `max_sequence_length = 1024`: Maksimum sıra uzunluğunu 1024 token olarak belirler.
2. `truncate_sequence` fonksiyonu: Girdi dizisini maksimum uzunluğa göre kısaltır.
3. `pack_samples` fonksiyonu: Birden fazla küçük örneği tek bir batch'e paketler.
4. `create_attention_mask` fonksiyonu: Paketlenmiş batch için dikkat maskesi oluşturur.

---

