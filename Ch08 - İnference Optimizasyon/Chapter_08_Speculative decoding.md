# Spekülatif Kod Çözme (Speculative Decoding)

Diğer bir güçlü optimizasyon tekniği, yardımcı üretim (assisted generation) olarak da adlandırılan spekülatif kod çözmedir. Temel fikir, sürekli toplu işlemeye (continuous batching) rağmen, token-by-token üretim sürecinin hızlandırıcının (accelerator) paralel işleme kapasitesini tam olarak doyuramadığıdır. Spekülatif kod çözme, bu boşta kalan işlem kapasitesini kullanarak daha küçük bir proxy modeli (proxy model) ile birden fazla token'ı aynı anda tahmin etmeyi amaçlar (bkz. Şekil 8.3).

Şekil 8.3 – Geleneksel kod çözme (sol) ve spekülatif kod çözme (sağ) gösterimi

Genel yaklaşım:
1. Ana modelin damıtılmış (distilled) veya budanmış (pruned) bir versiyonu gibi daha küçük bir modeli, birden fazla token tamamlamasını paralel olarak tahmin etmek için uygula. Bu, tek bir adımda 5-10 token'ın tahmin edilmesi olabilir.
2. Bu spekülatif tamamlamaları, büyük modelin ne üreteceğini doğrulamak için tam modele (full model) ver. 
3. Spekülatif tamamlamalardan en uzun eşleşen ön eki (matching prefix) sakla ve yanlış tokenları at.
4. Sonuç olarak, eğer küçük model büyük modeli iyi yaklıyorsa, birden fazla token tek bir adımda üretilebilir. Bu, pahalı büyük modelin birkaç iterasyon için çalıştırılmasını önler.

Hızlanma derecesi, küçük modelin tahminlerinin kalitesine bağlıdır - %90 eşleşme 3-4 kat hızlanma sağlayabilir. Her iki modelin de aynı tokenizer'ı (tokenizer) kullanması çok önemlidir. Eğer durum böyle değilse, taslak model tarafından üretilen tokenlar büyük model tarafından üretilenlerle uyumlu olmayacaktır, bu da onları uyumsuz hale getirir.

### Kod Uygulaması

Bu teknik transformers kütüphanesi kullanılarak uygulanabilir. Bu örnekte, Alibaba Cloud'dan iki Qwen1.5 modeli kullanılacaktır: 1.8B versiyonu ana model olarak ve 0.5B versiyonu taslak model (draft model) olarak.

```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM

# Model kimlikleri
model_id = "Qwen/Qwen1.5-1.8B-Chat"
draft_model_id = "Qwen/Qwen1.5-0.5B-Chat"

# Tokenizer ve modelleri yükle
tokenizer = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForCausalLM.from_pretrained(model_id, device_map="auto")
draft_model = AutoModelForCausalLM.from_pretrained(draft_model_id, device_map="auto")

# Cihazı belirle (cuda veya cpu)
device = "cuda" if torch.cuda.is_available() else "cpu"

# Girişi tokenizer ile işle ve hızlandırıcıya (accelerator) yükle
inputs = tokenizer("What is 2+2?", return_tensors="pt").to(device)

# Spekülatif kod çözmeyi etkinleştirmek için model.generate() fonksiyonunu assistant_model argümanı ile kullan
outputs = model.generate(**inputs, do_sample=True, assistant_model=draft_model, temperature=0.7, max_new_tokens=64)

# Çıktıyı decode et ve yazdır
print(tokenizer.batch_decode(outputs, skip_special_tokens=True))
```

### Kod Açıklaması

1. **İlgili kütüphanelerin import edilmesi**: `torch` ve `transformers` kütüphaneleri import edilir.
2. **Model ve tokenizer'ın yüklenmesi**: `AutoTokenizer` ve `AutoModelForCausalLM` sınıfları kullanılarak model ve tokenizer yüklenir. `device_map="auto"` parametresi modelin uygun cihazda (GPU veya CPU) çalışmasını sağlar.
3. **Giriş verisinin işlenmesi**: `tokenizer` kullanılarak giriş metni tokenlara dönüştürülür ve uygun cihazda saklanır.
4. **`model.generate()` fonksiyonunun kullanılması**: Spekülatif kod çözmeyi etkinleştirmek için `assistant_model` parametresi ile `draft_model` belirtilir. `do_sample=True` ve `temperature=0.7` parametreleri üretimin daha çeşitli ve yaratıcı olmasına yardımcı olur. `max_new_tokens=64` parametresi üretilen token sayısını sınırlar.
5. **Çıktının decode edilmesi ve yazdırılması**: `tokenizer.batch_decode()` fonksiyonu kullanılarak üretilen tokenlar metne dönüştürülür ve yazdırılır.

### Prompt Lookup Decoding

Prompt lookup decoding, spekülatif kod çözmenin bir varyantıdır ve özetleme gibi girdi tabanlı görevler için uyarlanmıştır. Paylaşılan n-gramlar (n-grams) LLM aday tokenları (LLM candidate tokens) olarak kullanılır.

```python
outputs = model.generate(**inputs, prompt_lookup_num_tokens=4)
```

### Sonuç

Statik KV önbelleği (static KV cache) ile `torch.compile` birleştirilerek, sürekli toplu işlemenin uygulanması ve spekülatif kod çözme tekniklerinin kullanılmasıyla, LLMs'ler 2-4 kat veya daha fazla hızlanma sağlayabilir. Spekülatif kod çözme TGI tarafından yerel olarak desteklenir.

---

