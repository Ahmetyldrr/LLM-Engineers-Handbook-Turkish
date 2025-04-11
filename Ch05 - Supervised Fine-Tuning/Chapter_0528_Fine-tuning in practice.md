#İleri Seviye İnce Ayar (Fine-Tuning) için Hazırlık

Şimdi, özel veri setimiz üzerinde açık kaynaklı bir modeli ince ayar (fine-tune) yapalım. Bu bölümde, verimlilik için LoRA ve QLoRA'yı uygulayan bir örnek göstereceğiz. Mevcut donanımınıza bağlı olarak, yapılandırmanızı en iyi karşılayan tekniği seçebilirsiniz. Görev veya alan-specific kullanım durumları için kullanabileceğimiz birçok verimli açık ağırlıklı (open-weight) model vardır. En alakalı LLM'yi (İngilizce teknik terim: Large Language Model) seçmek için üç ana parametreyi dikkate almamız gerekir: 
Lisans : Bazı model lisansları yalnızca ticari olmayan çalışmaları (non-commercial work) izin verir, bu da bir şirket için ince ayar yapmak istiyorsak bir problemdir. Bu alanda özel lisanslar yaygındır ve örneğin belirli sayıda kullanıcıya sahip şirketleri hedefleyebilir.
Bütçe : Daha küçük parametre boyutlarına sahip modeller (<10 B), daha büyük modellere göre ince ayar yapmak ve çıkarım (inference) için dağıtmak çok daha ucuzdur. Bunun nedeni, daha ucuz GPU'larda çalışabilmeleri ve saniyede daha fazla token işleyebilmeleridir.
Performans : Temel modeli genel amaçlı ölçütlerde (general-purpose benchmarks) veya daha iyisi, son kullanım durumuyla ilgili alan veya görev-specific ölçütlerde değerlendirmek çok önemlidir. Bu, modelin ince ayar yaptıktan sonra amaçlanan görevlerde iyi performans göstermek için gerekli yeteneklere sahip olmasını sağlar.

Bu bölümde, Meta tarafından yayınlanan açık ağırlıklı bir model olan Llama 3.1 8B'yi seçeceğiz. Ticari kullanımına izin veren özel bir lisansa (“Llama 3.1 Community License Agreement”) sahiptir. 8B parametre ile çoğu GPU'ya sığacak kadar küçüktür ve rakiplerine kıyasla yüksek bir performans seviyesine ulaşır. Bunu Open LLM Leaderboard ve model kartında ayrıntılı olarak verilen diğer ölçütleri kullanarak doğrulayabiliriz.

Modelleri ince ayarlamak için özel araçlar ve kütüphaneler vardır. Özellikle aşağıdaki kütüphaneleri öneriyoruz:
- **TRL (Transformer Reinforcement Learning)**: Hugging Face tarafından oluşturulan ve sürdürülen, SFT (Supervised Fine-Tuning) ve tercih hizalaması (preference alignment) kullanarak LLM'leri eğitmek için bir kütüphanedir. Popüler ve güvenilir bir kütüphanedir ve algoritmalar açısından en güncel olan olma eğilimindedir. FSDP (Fully Sharded Data Parallel) ve DeepSpeed ile tek ve çoklu GPU ayarlarında çalışır.
- **Axolotl**: Wing Lian tarafından oluşturulan bu araç, yeniden kullanılabilir YAML yapılandırma dosyaları ile LLM'lerin ince ayarını kolaylaştırır. TRL üzerine kuruludur ancak çeşitli formatlarda depolanan veri kümelerini otomatik olarak birleştirme gibi birçok ek özellik içerir. Ayrıca FSDP ve DeepSpeed ile tek ve çoklu GPU ayarlarını destekler.
- **Unsloth**: Daniel ve Michael Han tarafından oluşturulan Unsloth, eğitimi hızlandırmak (2-5 kat) ve bellek kullanımını azaltmak ( %80'e kadar daha az bellek) için özel çekirdekler kullanır. TRL üzerine kuruludur ve modelleri GGUF niceleme (quantization) formatına otomatik olarak dönüştürme gibi birçok yardımcı program sağlar. Yazıldığı sırada, yalnızca tek GPU ayarları için mevcuttur.

Verimliliği en üst düzeye çıkarmak için Unsloth kütüphanesini kullanarak ince ayar yapacağız. Aşağıdaki kod, LLMOps (Large Language Model Operations) boru hattımızın bir parçası olarak tasarlanmıştır, ancak bağımsız bir komut dosyası olarak da kullanılabilir. Ayrıca SageMaker, bulut GPU'ları (Lambda Labs veya RunPod gibi), Google Colab ve diğer birçok ortamda çalıştırılabilir. Bunu farklı GPU'larda (A40, A100 ve L4 gibi) test ettik.

Unsloth kütüphanesini ve bağımlılıklarını yüklemek için, kitabın GitHub deposundan (https://github.com/PacktPublishing/LLM-Engineering) veya Unsloth'un deposundan (https://github.com/unslothai/unsloth) doğrudan yüklemeyi öneririz. Bu yaklaşım, bağımlılıklarla olası çakışmaları ele almak için düzenli olarak güncellenen kurulum adımları nedeniyle önerilir.

İlk olarak, bir gated modeline erişmek ve (isteğe bağlı olarak) ince ayarlanmış modelimizi Hugging Face'e (https://huggingface.co/) yüklemek istiyoruz. Bu, bir hesaba giriş yapmayı gerektirir. Bir hesabınız yoksa, bir tane oluşturabilir ve API anahtarınızı (Settings | Access Tokens | Create new token) `.env` dosyasına saklayabilirsiniz:
```
HF_TOKEN=YOUR_API_KEY
```
Comet ML API anahtarınızın da `.env` dosyasına olduğundan emin olun:
```
COMET_API_KEY=YOUR_API_KEY
```
Gerekli tüm paketleri içe aktarın:
```python
import os
import torch
from trl import SFTTrainer
from datasets import load_dataset, concatenate_datasets
from transformers import TrainingArguments, TextStreamer
from unsloth import FastLanguageModel, is_bfloat16_supported
```
Şimdi, ince ayar yapmak için modeli ve karşılık gelen tokenizer'ı yükleyelim. Unsloth'un `FastLanguageModel` sınıfını `.from_pretrained()` metodu ile kullanıyoruz. Model adının yanı sıra, maksimum dizi uzunluğunu (bu örnekte 2.048) belirtmemiz gerekir. Son olarak, `load_in_4bit` argümanı, QLoRA (quantized pre-trained weights) veya LoRA'yı kullanıp kullanmayacağımızı gösterir. Bu örnekte daha hızlı eğitim ve daha yüksek kalite nedeniyle LoRA'yı kullanacağız, ancak VRAM gereksinimlerini karşılamıyorsanız QLoRA'ya kolayca geçebilirsiniz.
```python
max_seq_length = 2048
model, tokenizer = FastLanguageModel.from_pretrained(
    model_name="meta-llama/Meta-Llama-3.1-8B",
    max_seq_length=max_seq_length,
    load_in_4bit=False,
)
```
Model yüklendikten sonra, LoRA yapılandırmamızı tanımlayabiliriz. Burada, yazı stilini taklit etmek ve talimat örneklerinden bilgiyi kopyalamak için yeterince büyük olan 32 rankını kullanıyoruz. Sonuçlarınızın hayal kırıklığı yaratması durumunda bu değeri 64 veya 128'e artırabilirsiniz. Ayrıca eğitimi hızlandırmak için alpha'yı 32 olarak ayarlıyoruz, dropout olmadan ve bias olmadan. Son olarak, ince ayar sürecinin kalitesini en üst düzeye çıkarmak için her doğrusal katmanı hedefliyoruz.
```python
model = FastLanguageModel.get_peft_model(
    model,
    r=32,
    lora_alpha=32,
    lora_dropout=0,
    target_modules=["q_proj", "k_proj", "v_proj", "up_proj", "down_proj", "o_proj", "gate_proj"],
)
```
Ardından, ince ayar için doğru formatta verileri hazırlamamız gerekir. Bu örnekte, `llmtwin` veri kümesinde çok fazla örnek yoktur (3.000 örnek). Bu, modelin sohbet şablonunu (chat template) doğru bir şekilde öğrenememesi sorununa neden olur. Bunu ele almak için, `FineTome` adlı yüksek kaliteli genel amaçlı bir veri kümesi ile örnek sayısını artıracağız. Bu, `arcee-ai/The-Tome` veri kümesinin `fineweb-edu-classifier` kullanılarak filtrelenmiş bir versiyonudur. Bu veri kümesinin 100.000 örneğini kullanmak yerine, yalnızca eğitim bölümünde 10.000 örnek istediğimizi belirleyeceğiz. Bu iki veri kümesini birleştirerek son veri setimizi oluşturuyoruz.
```python
dataset1 = load_dataset("mlabonne/llmtwin")
dataset2 = load_dataset("mlabonne/FineTome-Alpaca-100k", split="train[:10000]")
dataset = concatenate_datasets([dataset1, dataset2])
```
Şimdi, bu verileri bir sohbet şablonu kullanarak biçimlendirmemiz gerekiyor. Kolaylık olması açısından Alpaca şablonunu kullanalım. Bu şablon ek tokenlere ihtiyaç duymaz, bu da onu daha az hataya eğilimli kılar (ancak ChatML'ye kıyasla performansı biraz etkileyebilir). Burada, tüm talimatları ve cevapları Alpaca şablonuna eşliyoruz. Modelin sonlandırma tokenini (EOS) çıkarmayı öğrenmesini sağlamak için her mesajın sonuna elle EOS tokenini ekliyoruz. Bu olmadan, sonsuza kadar cevap üretmeye devam edecektir.
```python
alpaca_template = """Below is an instruction that describes a task. Write a response that appropriately completes the request.
### Instruction: {}
### Response: {}"""
EOS_TOKEN = tokenizer.eos_token
dataset = dataset.map(format_samples, batched=True, remove_columns=dataset.column_names)
```
Veri kümesi hazırlandıktan sonra, eğitim ( %95) ve test ( %5) kümelerine ayırabiliriz.
```python
dataset = dataset.train_test_split(test_size=0.05)
```
Model şimdi eğitilmeye hazır. `SFTTrainer()` sınıfı, eğitim için tüm hiperparametreleri saklar. Ayrıca model, tokenizer, LoRA yapılandırması ve veri kümelerini sağlarız. Önceki bölümdeki önerileri takiben, 3e-4'lük bir öğrenme oranı ile doğrusal bir zamanlayıcı (linear scheduler) ve maksimum dizi uzunluğu 2048 ile eğitiyoruz. Bu modeli üç epoch ile 2 batch boyutu ve 8 gradyan birikim adımı (etkili batch boyutu 16 için) ile eğitiyoruz. Ayrıca 0.01 ağırlık_decay ile `adamw_8bit` optimizer'ı seçiyoruz. Kullandığımız GPU'ya bağlı olarak, aktivasyonlar için otomatik olarak FP16 veya BF16 kullanacaktır. Son olarak, deney takibi için Comet ML'ye eğitim çalıştırmamızı bildiriyoruz.
```python
trainer = SFTTrainer(
    model=model,
    tokenizer=tokenizer,
    train_dataset=dataset["train"],
    eval_dataset=dataset["test"],
    dataset_text_field="text",
    max_seq_length=max_seq_length,
    dataset_num_proc=2,
    packing=True,
    args=TrainingArguments(
        learning_rate=3e-4,
        lr_scheduler_type="linear",
        per_device_train_batch_size=2,
        gradient_accumulation_steps=8,
        num_train_epochs=3,
        fp16=not is_bfloat16_supported(),
        bf16=is_bfloat16_supported(),
        logging_steps=1,
        optim="adamw_8bit",
        weight_decay=0.01,
        warmup_steps=10,
        output_dir="output",
        report_to="comet_ml",
        seed=0,
    ),
)
trainer.train()
```
Bu modeli birleştirilmiş veri kümesi üzerinde eğitmek birkaç saat sürebilir. Örneğin, A100 GPU'da 50 dakika sürer. Tamamlandığında, hızlı bir örnekle test edebiliriz. Amaç, ince ayarlanmış modeli doğru bir şekilde değerlendirmek değil, tokenizer veya sohbet şablonuyla ilgili bariz hataların olmadığını doğrulamaktır. Hızlı çıkarım için Unsloth'tan `FastLanguageModel.for_inference()` kullanabiliriz. Direkt olarak Alpaca formatında bir talimatı biçimlendiriyoruz. Kullanıcı talimatının sonuna asistan başlığını (### Response:) eklemek için boş bir cevap sağladığımızı unutmayın. Bu, modeli talimatı tamamlamak yerine cevap vermeye zorlar. Ayrıca, oluşturmayı yazdırmadan önce tamamlamak için beklemek yerine bir metin akışı (text streamer) kullanıyoruz.
```python
FastLanguageModel.for_inference(model)
message = alpaca_prompt.format("Write a paragraph to introduce supervised fine-tuning.", "")
inputs = tokenizer([message], return_tensors="pt").to("cuda")
text_streamer = TextStreamer(tokenizer)
_ = model.generate(**inputs, streamer=text_streamer, max_new_tokens=256, use_cache=True)
```
Modelimiz tarafından sağlanan cevap:
```
Supervised fine-tuning is a method used to enhance a language model by providing it with a curated dataset of instructions and their corresponding answers. This process is designed to align the model's responses with human expectations, thereby improving its accuracy and relevance. The goal is to ensure that the model can respond effectively to a wide range of queries, making it a valuable tool for applications such as chatbots and virtual assistants.
```
Bu doğru ve Alpaca sohbet şablonuna uygun bir şekilde biçimlendirilmiştir.

Artık modelimiz başarıyla ince ayarlandıktan sonra, yerel olarak ve/veya Hugging Face Hub'a aşağıdaki fonksiyonları kullanarak yükleyebiliriz.
```python
model.save_pretrained_merged("model", tokenizer, save_method="merged_16bit")
model.push_to_hub_merged("mlabonne/TwinLlama-3.1-8B", tokenizer, save_method="merged_16bit")
```
Tebrikler, bir temel modeli sıfırdan ince ayarladınız! Eğitim sırasında, Comet ML'ye giderek eğitim kaybınızı, doğrulama kaybınızı ve diğer birçok metriği izleyebilirsiniz.

### Kod Açıklamaları

1. **Unsloth Kütüphanesini Yükleme ve Modeli Hazırlama**
   - Unsloth kütüphanesini ve bağımlılıklarını yüklemek için GitHub deposu önerilir.
   - `HF_TOKEN` ve `COMET_API_KEY` gibi API anahtarları `.env` dosyasına saklanır.

2. **Gerekli Paketleri İçe Aktarma**
   - `torch`, `trl`, `datasets`, `transformers`, ve `unsloth` gibi kütüphaneler içe aktarılır.

3. **Model ve Tokenizer'ı Yükleme**
   - `FastLanguageModel.from_pretrained()` ile model ve tokenizer yüklenir.
   - `max_seq_length` ve `load_in_4bit` gibi parametreler yapılandırılır.

4. **LoRA Yapılandırması**
   - `FastLanguageModel.get_peft_model()` ile LoRA yapılandırması tanımlanır.
   - `r`, `lora_alpha`, `lora_dropout`, ve `target_modules` gibi parametreler ayarlanır.

5. **Veri Kümesini Hazırlama**
   - `load_dataset()` ile veri kümeleri yüklenir ve `concatenate_datasets()` ile birleştirilir.
   - `map()` fonksiyonu ile veri kümesi Alpaca şablonuna göre biçimlendirilir.

6. **Eğitim ve Test Kümelerine Ayırma**
   - `train_test_split()` ile veri kümesi eğitim ve test kümelerine ayrılır.

7. **Eğitim**
   - `SFTTrainer()` sınıfı ile eğitim için hiperparametreler yapılandırılır.
   - `train()` metodu ile model eğitilir.

8. **Modeli Test Etme**
   - `FastLanguageModel.for_inference()` ile model çıkarım için hazırlanır.
   - `generate()` metodu ile modelden bir cevap üretilir.

9. **Modeli Kaydetme ve Yükleme**
   - `save_pretrained_merged()` ve `push_to_hub_merged()` ile model yerel olarak ve Hugging Face Hub'a yüklenir.

---

