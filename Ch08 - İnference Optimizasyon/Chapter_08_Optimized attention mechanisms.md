# Transformer Mimarisi ve PagedAttention

Transformer mimarisi, girdi tokenlerinin (veya dizi uzunluğunun) sayısıyla kuadratik olarak ölçeklenen dikkat mekanizmasına (attention mechanism) dayanır. Bu, özellikle KV önbelleğinin (KV cache) boyutunun şişebileceği daha uzun diziler için verimsizdir. Kwon, Li ve diğerleri (2023) tarafından tanıtılan PagedAttention, işletim sistemlerindeki sanal bellek ve sayfalama (paging) konseptlerinden ilham alarak bu bellek zorluklarını ele alır. KV önbelleğini bloklara ayırarak bitişik bellek tahsisine (contiguous memory allocation) olan ihtiyacı ortadan kaldırır. Her blok, sabit sayıda token için anahtarları ve değerleri içerir. Dikkat hesaplama sırasında, PagedAttention çekirdeği (kernel), bu blokları fiziksel bellek konumlarına bakılmaksızın verimli bir şekilde getirir. Bu bölümleme, optimuma yakın bellek kullanımını sağlar. Bu, daha fazla diziyi birlikte toplamak için kullanışlıdır, bu da verimi ve GPU kullanımını artırır. Ayrıca, PagedAttention'ın blok tabanlı yaklaşımı, aynı istemden (prompt) oluşturulan birden fazla çıktı dizisi arasında bellek paylaşımını doğal olarak destekler. Bu, özellikle aynı istemin birden fazla çıktı oluşturmak için kullanıldığı paralel örnekleme (parallel sampling) ve ışın arama (beam search) gibi durumlarda avantajlıdır. Paylaşılan bellek blokları, tekrarlayan hesaplamaları ve bellek kullanımını azaltarak, yazarlara göre bellek yükünü %55'e kadar azaltır ve verimi 2,2 kata kadar artırır. vLLM kütüphanesi, PagedAttention'ın ilk uygulamasını aldı. O zamandan beri, PagedAttention ayrıca TGI ve TensorRT-LLM'de de uygulanmıştır.

```python
# Kod örneği yok
```

# FlashAttention-2

Diğer bir popüler seçenek ise FlashAttention-2'dir. Tri Dao (2023) tarafından geliştirilen bu yöntem, geleneksel dikkat mekanizmasındaki kuadratik çalışma zamanı ve bellek kısıtlamalarını ele almak için tasarlanmış birkaç önemli yenilik sunar. Girdi ve çıktı matrislerini daha küçük bloklara ayırarak, FlashAttention-2 bu blokların GPU'nun yonga üstü SRAM'ine (on-chip SRAM) sığmasını sağlar, bu da yüksek bant genişliği belleğe (high-bandwidth memory) göre çok daha hızlıdır. Bu yaklaşım, GPU'nun ana belleği ile işleme birimleri arasındaki veri transferlerinin sıklığını önemli ölçüde azaltır. Bu, çevrimiçi softmax (online softmax) ile birleştirilir, bu da dikkat puanları matrisinin her bloğu için softmax fonksiyonunu bağımsız olarak hesaplar, tüm matris için bir kerede hesaplamak yerine. Çalışan maksimum ve üstel toplamları tutarak, FlashAttention-2 dikkat olasılıklarını büyük ara matrisleri saklamadan hesaplayabilir. Ek olarak, FlashAttention-2'nin çevrimiçi softmax hesaplaması, blok bazlı işlemeyi mümkün kılar, doğruluğu korurken bellek gereksinimlerini önemli ölçüde azaltır. Bu, özellikle eğitim için önemlidir, burada ara değerlerin yeniden hesaplanması (saklamak yerine) geri geçişte bellek kullanımını dizi uzunluğuna göre kuadratikten lineere düşürür.

```python
# FlashAttention-2'yi yüklemek için
pip install flash-attn --no-build-isolation

# FlashAttention-2'yi kullanarak model yüklemek için
from transformers import AutoModelForCausalLM
model = AutoModelForCausalLM.from_pretrained(
    "mistralai/Mistral-7B-Instruct-v0.3",
    attn_implementation="flash_attention_2",
)
```

## Kod Açıklaması

1. `pip install flash-attn --no-build-isolation`: FlashAttention-2 kütüphanesini yükler. `--no-build-isolation` flag'ı, bağımlılıkların yüklenmesini engeller.
2. `from transformers import AutoModelForCausalLM`: Transformers kütüphanesinden `AutoModelForCausalLM` sınıfını içe aktarır.
3. `model = AutoModelForCausalLM.from_pretrained(...)`: `mistralai/Mistral-7B-Instruct-v0.3` modelini önceden eğitilmiş ağırlıklarla yükler.
4. `attn_implementation="flash_attention_2"`: Dikkat mekanizmasının uygulanmasını FlashAttention-2 olarak belirtir.

Bu kod, FlashAttention-2 kullanarak bir model yükler ve dikkat mekanizmasını optimize eder.

---

