#SFT (Supervised Fine-Tuning)

SFT, önceden eğitilmiş modelleri (pre-trained models), talimat çiftleri (instruction pairs) ve cevaplarından oluşan daha küçük bir veri seti (dataset) üzerinde yeniden eğitmekten (re-training) oluşur. SFT'nin amacı, yalnızca sonraki token tahmini (next-token prediction) yapabilen bir temel modeli (base model), soruları cevaplayabilen ve talimatları takip edebilen (following instructions) kullanışlı bir asistan haline getirmektir. SFT, temel modelin genel performansını (general-purpose SFT) artırmak, yeni bilgiler kazandırmak (örneğin, yeni diller, alanlar, vb.), belirli görevlere odaklanmak (specific tasks), belirli bir ses tonunu benimsemek (adopt a particular voice) ve daha fazlası için de kullanılabilir. Bu bölümde, ince ayar (fine-tuning) ne zaman kullanılacağını tartışacağız ve depolama formatları (storage formats) ve sohbet şablonları (chat templates) ile ilgili kavramları inceleyeceğiz. Son olarak, SFT'yi uygulamanın üç popüler yolunu tanıtacağız: tam ince ayar (full-finetuning), Düşük Dereceli Uyarlama (LoRA - Low-Rank Adaptation) ve Kuantizasyon Farkında Düşük Dereceli Uyarlama (QLoRA - Quantization-aware Low-Rank Adaptation).

**Kodlar bulunmamaktadır, ancak metnin kendisi bir kod açıklaması değildir. Ancak, aşağıdaki gibi bir kod örneği varsayalım:**

```python
# Örnek kod
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

# Model ve tokenizer yükleme
model_name = "base_model"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name)

# SFT için veri seti yükleme
dataset = load_dataset("instruction_dataset")

# SFT işlemi
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model.to(device)
for batch in dataset:
    input_ids = batch["input_ids"].to(device)
    attention_mask = batch["attention_mask"].to(device)
    labels = batch["labels"].to(device)
    optimizer = torch.optim.Adam(model.parameters(), lr=1e-5)
    optimizer.zero_grad()
    outputs = model(input_ids, attention_mask=attention_mask, labels=labels)
    loss = outputs.loss
    loss.backward()
    optimizer.step()
```

**Kodun ayrıntılı açıklaması:**

1. `import torch`: PyTorch kütüphanesini içe aktarır.
2. `from transformers import AutoModelForCausalLM, AutoTokenizer`: Transformers kütüphanesinden `AutoModelForCausalLM` ve `AutoTokenizer` sınıflarını içe aktarır.
3. `model_name = "base_model"`: Kullanılacak temel modelin adını belirler.
4. `tokenizer = AutoTokenizer.from_pretrained(model_name)`: Belirtilen model için tokenizer'ı yükler.
5. `model = AutoModelForCausalLM.from_pretrained(model_name)`: Belirtilen model için Causal LM modelini yükler.
6. `dataset = load_dataset("instruction_dataset")`: SFT için kullanılacak veri setini yükler.
7. `device = torch.device("cuda" if torch.cuda.is_available() else "cpu")`: Uygun cihazı (GPU veya CPU) seçer.
8. `model.to(device)`: Modeli seçilen cihaza taşır.
9. `for batch in dataset`: Veri setindeki her bir batch için döngü başlatır.
10. `input_ids = batch["input_ids"].to(device)`: Giriş IDs'lerini cihaza taşır.
11. `attention_mask = batch["attention_mask"].to(device)`: Dikkat maskelerini cihaza taşır.
12. `labels = batch["labels"].to(device)`: Etiketleri cihaza taşır.
13. `optimizer = torch.optim.Adam(model.parameters(), lr=1e-5)`: Adam optimizer'ı oluşturur.
14. `optimizer.zero_grad()`: Gradyanları sıfırlar.
15. `outputs = model(input_ids, attention_mask=attention_mask, labels=labels)`: Modeli çalıştırır.
16. `loss = outputs.loss`: Kaybı hesaplar.
17. `loss.backward()`: Gradyanları geriye doğru hesaplar.
18. `optimizer.step()`: Optimizer'ı günceller.

---

