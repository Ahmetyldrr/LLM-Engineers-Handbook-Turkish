#LLM'lerin (Large Language Models) Üretim Sürecini Optimize Etme
LLM'lerin metni token token ürettiğini gördük, bu yavaş çünkü her yeni tahmin bir önceki bağlama (context) bağlıdır. Örneğin, bir dizideki 100. tokeni tahmin etmek için model, 1'den 99'a kadar olan tokenlerin bağlamına ihtiyaç duyar. 101. tokeni tahmin ederken, yine 1'den 99'a kadar olan tokenlerin yanı sıra 100. tokenin bilgisine de ihtiyaç duyar. Bu tekrarlanan hesaplama özellikle verimsizdir. Anahtar-değer (KV) önbelleği (cache), bu sorunu self-attention katmanları tarafından üretilen anahtar-değer çiftlerini depolayarak çözer. Her yeni token için bu çiftleri yeniden hesaplamak yerine, model bunları önbellekten alır, böylece üretimi önemli ölçüde hızlandırır. Bu tekniğin bir gösterimini Şekil 8.2'de görebilirsiniz:

Şekil 8.2 – KV Önbelleğinin (KV Cache) Gösterimi

Yeni bir token üretildiğinde, yalnızca o tek token için anahtar ve değer hesaplanmalı ve önbelleğe eklenmelidir. KV önbelleği, her popüler araç ve kütüphanede uygulanan anında bir optimizasyondur. Bazı uygulamalar, modelin her katmanı için ayrı bir KV önbelleği tutar.

KV önbelleğinin boyutu, token sayısına (sequence length) ve birkaç model boyutuna, örneğin katman sayısına (number of layers), dikkat başlıkları sayısına (number of attention heads), bunların boyutuna (dimension) ve parametrelerin byte cinsinden hassasiyetine (precision) bağlıdır:

```python
# KV Önbellek Boyutu Hesaplanması
# KV_cache_size = sequence_length * num_layers * num_attention_heads * dimension * precision_in_bytes
```

Tipik bir 7B parametreli model için 16-bit hassasiyet kullanıldığında, bu yüksek dizi uzunlukları (2048 tokenden yüksek) için 2 GB'ı aşar. Daha fazla katman ve daha yüksek embedding boyutlarına sahip daha büyük modeller, daha da büyük bellek gereksinimleri görecektir.

KV önbelleği her üretim adımıyla büyüdüğü ve dinamik olduğu için, PyTorch kodunu hızlı ve optimize edilmiş çekirdeklere (kernels) birleştiren güçlü bir optimizasyon aracı olan `torch.compile`'dan yararlanmanızı engeller.

Statik KV önbelleği, KV önbellek boyutunu maksimum bir değere önceden tahsis ederek bu sorunu çözer, böylece `torch.compile` ile birleştirerek ileri geçişte (forward pass) 4 kata kadar hızlanma sağlar.

Bir modeli, transformers kütüphanesini kullanarak statik KV önbelleği ile yapılandırmak için şu adımları izleyin:

### Kod
```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM

# Model ve Tokenizer'ı İçe Aktarma
model_id = "google/gemma-2b-it"
tokenizer = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForCausalLM.from_pretrained(model_id, device_map="auto")

# Statik Önbelleği Uygulama
model.generation_config.cache_implementation = "static"

# Modeli Derleme (Compile)
compiled_model = torch.compile(model, mode="reduce-overhead", fullgraph=True)

# Girişleri Tokenize Etme ve Cihazı Seçme (GPU veya CPU)
device = "cuda" if torch.cuda.is_available() else "cpu"
inputs = tokenizer("What is 2+2?", return_tensors="pt").to(device)

# Çıktıyı Üretme ve Kod Çözme
outputs = model.generate(**inputs, do_sample=True, temperature=0.7, max_new_tokens=64)
print(tokenizer.batch_decode(outputs, skip_special_tokens=True))
# ['What is 2+2?\n\nThe answer is 4. 2+2 = 4.']
```

### Kodun Açıklaması

1. **İçe Aktarma**: Gerekli kütüphaneler ve model/tokenizer içe aktarılır.
   - `torch`: PyTorch kütüphanesi.
   - `AutoTokenizer` ve `AutoModelForCausalLM`: Transformers kütüphanesinden otomatik tokenizer ve nedensel dil modeli (causal language model) için kullanılan sınıflar.

2. **Model ve Tokenizer'ı İçe Aktarma**: Belirtilen model kimliği (`model_id`) ile önceden eğitilmiş tokenizer ve model içe aktarılır.
   - `device_map="auto"`: Modelin otomatik olarak uygun cihaza (GPU veya CPU) taşınmasını sağlar.

3. **Statik Önbelleği Uygulama**: Modelin üretim konfigürasyonunda (`generation_config`) önbellek uygulamasını "static" olarak ayarlayarak statik KV önbelleğini etkinleştirir.

4. **Modeli Derleme (Compile)**: `torch.compile` kullanarak modeli derler. 
   - `mode="reduce-overhead"`: Ekstra yükü azaltmak için optimize eder.
   - `fullgraph=True`: Tüm grafiği derler.

5. **Girişleri Tokenize Etme ve Cihazı Seçme**: Giriş metnini (`"What is 2+2?"`) tokenize eder ve uygun cihaza (GPU veya CPU) taşır.

6. **Çıktıyı Üretme ve Kod Çözme**: Tokenize edilmiş girişi kullanarak modelin çıktısını üretir ve daha sonra bu çıktıyı decode ederek yazdırır.
   - `do_sample=True`: Örnekleme yapar.
   - `temperature=0.7`: Örnekleme sıcaklığını ayarlar.
   - `max_new_tokens=64`: Üretilecek maksimum yeni token sayısını belirler.

Bu kod, LLM'lerin üretim sürecini optimize etmek için statik KV önbelleği kullanmayı ve `torch.compile` ile modelin derlenmesini gösterir.

---

