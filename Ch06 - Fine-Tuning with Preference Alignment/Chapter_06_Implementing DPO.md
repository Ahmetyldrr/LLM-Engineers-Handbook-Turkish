#DPO ile TwinLlama-3.1-8B Modelinin İnce Ayarlanması (Fine-Tuning)

Bu bölümde, 5. Bölüm'de oluşturduğumuz TwinLlama-3.1-8B modelini DPO (Direct Preference Optimization) ile ince ayarlayacağız (fine-tune). Kullanım kolaylığı ve performansı en üst düzeye çıkarmak için yine Unsloth kütüphanesini kullanacağız. Mevcut VRAM (Video Random Access Memory)'a bağlı olarak, LoRA (daha yüksek kalite, hız ve VRAM kullanımı) ve QLoRA (daha düşük kalite, hız ve VRAM kullanımı) arasında seçim yapabilirsiniz. Bu teknik, diğer tercih hizalama algoritmaları (preference alignment algorithms) ile birlikte TRL (Transformer Reinforcement Learning) ve Axolotl'de de mevcuttur. Bu örnek, DPO'nun gelişmiş bir uygulaması olarak görülebilir. Aslında, bir yazı stilini taklit etme amacımız, DPO'nun resmi dili teşvik etme doğal eğilimi ile çelişmektedir. Bu kısmen, seçilen cevapların (chosen answers) reddedilenlerden (rejected answers) daha resmi olmasından kaynaklanmaktadır. Pratikte bu, düşük bir öğrenme oranı (learning rate) ve epoch sayısı ile hafif ince ayar yapmamızı gerektirecektir. En iyi hiperparametreleri (hyperparameters) bulmak için 20'den fazla model eğittiğimiz ve bir dizi soru üzerinde çıktılarını karşılaştırdığımız bir deney gerçekleştirdik. Bu, bu görev için en iyi çalışan model ve parametreleri seçmemizi sağladı.

Bağımlılıklar (dependencies), 5. Bölüm'deki SFT (Supervised Fine-Tuning) ile aynıdır ve kitabın GitHub deposunda (https://github.com/PacktPublishing/LLM-Engineering) veya Unsloth'un deposunda (https://github.com/unslothai/unsloth) bulunabilir:

İlk olarak, gated model'e erişmek ve (isteğe bağlı olarak) ince ayarlanmış modelimizi Hugging Face'e (https://huggingface.co/) yüklemek istiyoruz. Bu, bir hesaba giriş yapmamızı gerektirir. Bir hesabınız yoksa, bir tane oluşturabilir ve API anahtarınızı (Settings | Access Tokens | Create new token) .env dosyasına saklayabilirsiniz: `HF_TOKEN = YOUR_API_KEY`
Comet ML API anahtarınızın da .env dosyasına eklediğinizden emin olun. Aksi takdirde, eğitim başladığında kod çökecek ve hata verecektir. `COMET_API_KEY = YOUR_API_KEY`

Gerekli tüm paketleri içe aktarmadan önce, TRL'den DPOTrainer sınıfı için bir yama (patch) uygulamak istiyoruz. Bu, notebook ortamlarında DPO günlüklerini (logs) düzeltir.
```python
from unsloth import PatchDPOTrainer
PatchDPOTrainer()
```
Artık diğer kütüphaneleri içe aktarabiliriz. DPO ve SFT arasındaki temel fark, DPO eğitimi için özel olan TRL'den DPOConfig ve DPOTrainer'ın içe aktarılmasıdır.
```python
import os
import torch
from datasets import load_dataset
from transformers import TrainingArguments, TextStreamer
from unsloth import FastLanguageModel, is_bfloat16_supported
from trl import DPOConfig, DPOTrainer
```
Bu adım, 5. Bölüm'den ince ayarlanmış modelimizi yükler. Aynı konfigürasyonu max_seq_length 2048 ile kullanıyoruz. QLoRA'yı etkinleştirmek için load_in_4bit'i True olarak ayarlayabilirsiniz. Aşağıda, artan hız ve kalite için LoRA DPO ince ayarını gerçekleştireceğiz.
```python
max_seq_length = 2048
model, tokenizer = FastLanguageModel.from_pretrained(
    model_name="mlabonne/TwinLlama-3.1-8B",
    max_seq_length=max_seq_length,
    load_in_4bit=False,
)
```
Şimdi modeli LoRA konfigürasyonu ile PEFT (Parameter-Efficient Fine-Tuning) için hazırlayalım. Rank (r) ve lora_alpha'yı 32'den (5. Bölüm'deki gibi) 64'e çıkarıyoruz. Bu, daha anlamlı ince ayar yapılmasına olanak tanıyacaktır. Hız için dropout'u 0 olarak tutuyoruz ve her lineer modülü hedefliyoruz.
```python
model = FastLanguageModel.get_peft_model(
    model,
    r=32,
    lora_alpha=32,
    lora_dropout=0,
    target_modules=["q_proj", "k_proj", "v_proj", "up_proj", "down_proj", "o_proj", "gate_proj"],
)
```
llmtwin-dpo veri setini (training split) yüklüyoruz, bu veri seti promptlarımızı, seçilen ve reddedilen cevapları içerir.
```python
dataset = load_dataset("mlabonne/llmtwin-dpo", split="train")
```
Veri hazırlama, 5. Bölüm'deki SFT örneğinden önemli ölçüde farklıdır. Burada, bir prompt, seçilen bir cevap ve reddedilen bir cevap içeren üçlüler (triples) vardır. format_samples fonksiyonunda, Alpaca sohbet şablonunu (chat template) her bir mesaja uygularız. Talimatın (instruction) sohbet formatını gerektiren tek şey olduğuna dikkat edin: seçilen ve reddedilen cevaplar sadece EOS (end of sentence) tokeni ile birleştirilmelidir. Son olarak, %95/%5 oranında bir eğitim/test bölünmesi (train/test split) oluştururuz.
```python
alpaca_template = """Below is an instruction that describes a task. Write a response that appropriately completes the request. ### Instruction: {} ### Response: """
EOS_TOKEN = tokenizer.eos_token

def format_samples(example):
    example["prompt"] = alpaca_template.format(example["prompt"])
    example["chosen"] = example['chosen'] + EOS_TOKEN
    example["rejected"] = example['rejected'] + EOS_TOKEN
    return {"prompt": example["prompt"], "chosen": example["chosen"], "rejected": example["rejected"]}

dataset = dataset.map(format_samples)
dataset = dataset.train_test_split(test_size=0.05)
```
Model ve veriler artık hazır, böylece ince ayara başlayabiliriz. SFT ile karşılaştırıldığında, ref_model ve beta gibi birkaç yeni parametre vardır. LoRA (veya QLoRA) kullandığımızdan, modeli doğrudan değil, adaptörleri eğitiyoruz. Bu, orijinal modeli (adaptörler olmadan) referans olarak kullanabileceğimiz anlamına gelir ve bu da önemli ölçüde VRAM tasarrufu sağlar. Beta parametresi, referans modelin önemini kontrol eder. Standart bir değer olan 0.1 çoğu senaryoda iyi çalışır, ancak deneylerimize dayanarak bunu 0.5'e yükseltmeye karar verdik. Bunun nedeni, eğitilen modelin düşük değerlerle resmi dil kullanmasıdır. Referans modele daha yakın olması bu sorunun çözülmesine yardımcı olur. Öğrenme oranı da daha düşüktür (SFT için 3e-4'ten burada 2e-6'ya). 3 epoch yerine 1 epoch için eğitiyoruz ve max_seq_length parametresi şimdi iki yeni parametreye ayrılmıştır: max_prompt_length (sadece prompt) ve max_length (prompt ve cevap). TrainingArguments sınıfını DPOConfig ile değiştirdiğimize dikkat edin.
```python
trainer = DPOTrainer(
    model=model,
    ref_model=None,
    tokenizer=tokenizer,
    beta=0.5,
    train_dataset=dataset["train"],
    eval_dataset=dataset["test"],
    max_length=max_seq_length//2,
    max_prompt_length=max_seq_length//2,
    args=DPOConfig(
        learning_rate=2e-6,
        lr_scheduler_type="linear",
        per_device_train_batch_size=2,
        per_device_eval_batch_size=2,
        gradient_accumulation_steps=8,
        num_train_epochs=1,
        fp16=not is_bfloat16_supported(),
        bf16=is_bfloat16_supported(),
        optim="adamw_8bit",
        weight_decay=0.01,
        warmup_steps=10,
        output_dir="output",
        eval_strategy="steps",
        eval_steps=0.2,
        logging_steps=1,
        report_to="comet_ml",
        seed=0,
    ),
)
trainer.train()
```
Model eğitildikten sonra, hızlı bir doğruluk kontrolü için çalıştırabiliriz. Bu adım, SFT örneğine benzer. Modeli çıkarım (inference) için hazırlar ve bir prompt'a cevap üretir.
```python
FastLanguageModel.for_inference(model)
message = alpaca_template.format("Write a paragraph to introduce supervised fine-tuning.", "")
inputs = tokenizer([message], return_tensors="pt").to("cuda")
text_streamer = TextStreamer(tokenizer)
_ = model.generate(**inputs, streamer=text_streamer, max_new_tokens=256, use_cache=True)
```
Eğitilen DPO modeli aşağıdaki cevabı verir:
Supervised fine-tuning is a method used to enhance the performance of pre-trained language models by utilizing labeled data. This technique involves taking a pre-trained model and refining it on a specific task, such as content creation or customer service. By providing the model with relevant data and guidance, it can learn to generate outputs that align more closely with the desired outcomes. This approach allows for the creation of more specialized models that can tackle complex tasks with greater accuracy and efficiency.

Bu cevabı SFT modelinin verdiği cevapla karşılaştırabiliriz:
Supervised fine-tuning is a method used to enhance a language model by providing it with a curated dataset of instructions and their corresponding answers. This process is designed to align the model's responses with human expectations, thereby improving its accuracy and relevance. The goal is to ensure that the model can respond effectively to a wide range of queries, making it a valuable tool for applications such as chatbots and virtual assistants.

DPO modeli, hem daha doğru hem de istenen yazı stiline daha yakın bir cevap sağlar. SFT için kaynak modeller olarak önceden eğitilmiş dil modellerini doğru bir şekilde tanımlar. Ayrıca, "insan beklentileri" ile hizalama yerine alan veya görev spesifik ince ayarları (domain or task-specific finetunes) belirtir, bu da tercih hizalama aşamasına daha yakındır. Cevap ayrıca daha az formeldir ve bir blog gönderisinde kullanacağımız bir şeydir.

Son olarak, son adım, eğitilen modeli yerel olarak kaydetmek ve Hugging Face Hub'a yüklemektir.
```python
model.save_pretrained_merged("model", tokenizer, save_method="merged_16bit")
```
Tebrikler! DPO modelimizi eğittik ve dışa aktardık. Şimdi Hugging Face Hub'da https://huggingface.co/mlabonne/TwinLlama-3.1-8B-DPO adresinde mevcuttur.

SFT ile karşılaştırıldığında, DPO'nun eğitim sırasında izlenmesi gereken birkaç ek metriği vardır. Şekil 6.6, ana metriklerle Comet ML kontrol panelini gösterir. Aşağıdaki URL'yi kullanarak halka açık olarak erişebilirsiniz: https://www.comet.com/mlabonne/llm-twin-training/

İnce ayarlanmış modelin performansını değerlendirmek için kullanılan metrikler:
- **Training loss**: Ortalama olarak sürekli olarak azalmasını istiyoruz. Sıfıra hızla düşmesi, modelin artık bir şey öğrenmediğini gösterir. Bu davranış, mutlaka aşırı uyuma (overfitting) veya kötü modellere yol açmaz, ancak yakından izlenmesi gerekir.
- **Validation loss**: Eğitim kaybına benzer şekilde, doğrulama kaybının da küçük bir farkla eğitim kaybını takip etmesini bekleriz.
- **Gradient norm**: Küçük gradyan normları ve birkaç ani artış beklüyoruz.
- **Rewards**: Seçilen ve reddedilen olmak üzere iki farklı ödülümüz var. Bunlar, eğitilen model ve referans model tarafından üretilen log olasılıkları arasındaki ortalama farka karşılık gelir. Zamanla, modelin seçilen cevapları seçmesini ve reddedilenleri reddetmesini bekleriz, bu da aralarındaki farkın artması anlamına gelir. Bu fark, **margins** metriği tarafından doğrudan izlenir ve seçilen ve reddedilen ödüller arasındaki fark olarak tanımlanır. İyi eğitilmiş bir modelin marjı hızla artacak ve sonra sabitlenecektir.
- **Accuracies**: Bu metrik, modelin seçilen cevapları doğru bir şekilde tanımladığı zamanların yüzdesini temsil eder. Bu doğruluk oranının eğitim sırasında kademeli olarak artmasını istiyoruz, ancak %100'e ulaşması gerekmez. Hatta, özellikle hızlı bir şekilde elde edilmişse, %100 doğruluk, tercih veri setinin model için çok kolay olduğunu gösterebilir. LLM hala bu tür bir veri setinden öğrenebilir, ancak daha zor örnekler eklemek faydalı olabilir.

Genel olarak, DPO'nun izlenmesi ve hata ayıklaması, daha karmaşık bir süreç olduğu için SFT'den biraz daha zordur ve bir referans model içerir. Ancak, PPO ve diğer RLHF algoritmalarından çok daha kolay kullanıma sahiptir. Yüksek kaliteli bir tercih veri setiniz ve güçlü bir ince ayarlanmış modeliniz olduğu sürece, farklı ranklar, beta parametreleri, öğrenme oranları ve epoch sayıları ile deneyler yaparak hangi deneyin tercihlerinizi en iyi şekilde yakaladığını görebilirsiniz.

---

