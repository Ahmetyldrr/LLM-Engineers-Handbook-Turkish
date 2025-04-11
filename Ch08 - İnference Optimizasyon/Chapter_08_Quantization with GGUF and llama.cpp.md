# Llama.cpp Projesi ve GGUF Quantization Teknikleri
Llama.cpp projesi, Georgi Gerganov tarafından oluşturulan, çeşitli LLMs (Large Language Models - Büyük Dil Modelleri) ile çıkarım (inference) gerçekleştirmek için tasarlanmış, açık kaynaklı bir C++ yazılım kitaplığıdır (library). Hugging Face Hub'da birçok nicemlenmiş (quantized) model bulunması nedeniyle en popüler nicemleme (quantization) tekniğidir. CUDA gibi donanım özgü kapalı kaynaklı (closed-source) kitaplıklara güvenen diğer kitaplıkların aksine, llama.cpp daha geniş bir donanım yelpazesinde çalışabilir. Özellikle uzmanlaşmış donanıma sahip olmayan kullanıcılar arasında büyük bir popülerlik kazanmıştır, çünkü CPU'larda ve Android cihazlarda çalışabilir. Ayrıca, llama.cpp katmanları GPU'ya aktarabilir (offload), böylece çıkarım hızını (inference speed) artırabilir. FlashAttention-2 ve speculative decoding gibi farklı çıkarım optimizasyon teknikleri (inference optimization techniques) ile uyumludur. Bu proje, model yüklemeyi (model loading) basitleştirmek ve hızlandırmak için tasarlanmış kendi nicemleme formatı (quantization format) GGUF'ya sahiptir. GGUF dosyaları, tensörleri (tensors) ve meta verileri (metadata) depolar ve 1-bit'ten 8-bit'e kadar çeşitli formatları destekler. Kullanılan bit sayısına ve belirli varyantlara göre bir adlandırma kuralı (naming convention) izler, örneğin: 
- IQ1_S ve IQ1_M : 1-bit hassasiyet (precision) – çok düşük kalite
- IQ2_XXS/XS/S/M ve Q2_K : 2-bit hassasiyet – genellikle düşük kalite ancak IQ2 büyük modeller için kullanılabilir
- IQ3_XXS/XS/S/M ve Q3_K_S/M/L : 3-bit hassasiyet – düşük kalite ancak büyük modeller için kullanılabilir
- IQ4_XS/NL ve Q4_K_S/M, Q4_0/1 : 4-bit hassasiyet – iyi kalite ve çoğu model için kullanılabilir
- Q5_K_S/M ve Q5_0/1 : 5-bit hassasiyet – yüksek kalite
- Q6_K : 6-bit hassasiyet – çok yüksek kalite
- Q8_0 : 8-bit hassasiyet – en yüksek kalite

GGUF nicemlemesine (quantization) kısa bir genel bakış sağlamak için, llama.cpp değerleri bloklara ayırır (groups values into blocks) ve bunları daha düşük bir hassasiyete (lower precision) yuvarlar (rounds). Örneğin, eski Q4_0 formatı, blok başına 32 değeri işler, bunları bloktaki en büyük ağırlık değerine (largest weight value) göre ölçeklendirir (scales) ve nicemler (quantizes). Q4_1'de, bloktaki en küçük değer (smallest value) de eklenir. Q4_K'de, ağırlıklar (weights) 32 değer içeren 8 blok içeren süper bloklara (super-blocks) ayrılır. Blok ölçekleri (block scales) ve minimum değerler (minimum values) de daha yüksek hassasiyette 6 bit ile nicemlenir. Son olarak, IQ4_XS gibi i-nicemleri (i-quants), QuIP# adlı başka bir nicemleme tekniğinden (quantization technique) esinlenmiştir. Bu, sekizli gruplarda (groups of eight) pozitif (veya negatif) nicem işaretlerinin (quant signs) eşit sayıda olmasını sağlar ve büyüklüklerini (magnitude) depolamak için kafes (lattice) yapısını uygular.

GGUF formatında bir modeli nicemleme (quantize) konusunda pratik bir örnek aşağıda verilmiştir. Aşağıdaki adımlar Google Colab'da ücretsiz bir T4 GPU'da çalıştırılabilir:

1. Llama.cpp ve gerekli kütüphanelerin kurulumu:
   ```bash
   !git clone https://github.com/ggerganov/llama.cpp
   !cd llama.cpp && git pull && make clean && LLAMA_CUBLAS=1 make
   !pip install -r llama.cpp/requirements.txt
   ```

2. Dönüştürmek istediğiniz modelin indirilmesi. Hugging Face Hub'dan model kimliğini (model ID) sağlayacağız – örneğin, mistralai/Mistral-7B-Instruct-v0.2:
   ```python
   MODEL_ID = "mlabonne/EvolCodeLlama-7b"
   MODEL_NAME = MODEL_ID.split('/')[-1]
   !git lfs install
   !git clone https://huggingface.co/{MODEL_ID}
   ```

3. Modelin FP16 formatına dönüştürülmesi. Bu, her GGUF nicemleme türü için kullanılacak ara bir üründür (intermediary artifact). Llama.cpp'de farklı dönüştürme betikleri (conversion scripts) mevcuttur ve farklı modellerle uyumludur:
   ```python
   fp16 = f"{MODEL_NAME}/{MODEL_NAME.lower()}.fp16.bin"
   !python llama.cpp/convert.py {MODEL_NAME} --outtype f16 --outfile {fp16}
   ```

4. Bir format seçilmesi (örneğin, Q4_K_M) ve nicemleme işleminin başlatılması. Bu işlem T4 GPU'da bir saat sürebilir:
   ```python
   METHOD = "q4_k_m"
   qtype = f"{MODEL_NAME}/{MODEL_NAME.lower()}.{METHOD.upper()}.gguf"
   !./llama.cpp/quantize {fp16} {qtype} {METHOD}
   ```

5. Nicemlenmiş modelin hazır hale gelmesi. Modeli yerel olarak indirebilir veya aşağıdaki kodları kullanarak Hugging Face Hub'a yükleyebilirsiniz:
   ```python
   from huggingface_hub import create_repo, HfApi
   
   hf_token = ""  # Belirtilen token
   username = ""  # Belirtilen kullanıcı adı
   
   api = HfApi()
   
   # Boş depo oluşturma
   create_repo(
       repo_id=f"{username}/{MODEL_NAME}-GGUF",
       repo_type="model",
       exist_ok=True,
       token=hf_token
   )
   
   # GGUF dosyalarını yükleme
   api.upload_folder(
       folder_path=MODEL_NAME,
       repo_id=f"{username}/{MODEL_NAME}-GGUF",
       allow_patterns="*.gguf",
       token=hf_token
   )
   ```

GGUF modelleri, llama-cpp-python gibi arka uçlarla (backends) ve LangChain gibi çerçevelerle (frameworks) kullanılabilir. Bu, nicemlenmiş bir modeli daha geniş bir sisteme entegre etmek istiyorsanız kullanışlıdır. Ayrıca, llama.cpp'nin hafif sunucusu (lightweight server), LM Studio ve Text Generation Web UI gibi ön uçları (frontends) kullanarak modelle doğrudan sohbet edebilirsiniz. Bu araçlar, GGUF modelleriyle kolay etkileşim sağlar ve ChatGPT'ye benzer bir deneyim sunar.

### Kodların Ayrıntılı Açıklaması

#### 1. Llama.cpp ve Gerekli Kütüphanelerin Kurulumu
```bash
!git clone https://github.com/ggerganov/llama.cpp
!cd llama.cpp && git pull && make clean && LLAMA_CUBLAS=1 make
!pip install -r llama.cpp/requirements.txt
```
- `git clone`: Llama.cpp projesini GitHub'dan klonlar.
- `git pull`: Llama.cpp projesini günceller.
- `make clean`: Önceki derlemelerden kalan dosyaları temizler.
- `LLAMA_CUBLAS=1 make`: Llama.cpp'yi CUBLAS (CUDA Basic Linear Algebra Subprograms) ile derler. Bu, GPU hızlandırmasını etkinleştirir.
- `pip install`: Llama.cpp tarafından gerekli Python kütüphanelerini kurar.

#### 2. Modelin İndirilmesi
```python
MODEL_ID = "mlabonne/EvolCodeLlama-7b"
MODEL_NAME = MODEL_ID.split('/')[-1]
!git lfs install
!git clone https://huggingface.co/{MODEL_ID}
```
- `MODEL_ID`: Hugging Face Hub'daki modelin kimliği.
- `MODEL_NAME`: Model kimliğinden modelin adını ayıklar.
- `git lfs install`: Git Large File Storage (LFS) kurulumunu yapar. Bu, büyük dosyaları yönetmek için gereklidir.
- `git clone`: Belirtilen modeli Hugging Face Hub'dan klonlar.

#### 3. Modelin FP16 Formatına Dönüştürülmesi
```python
fp16 = f"{MODEL_NAME}/{MODEL_NAME.lower()}.fp16.bin"
!python llama.cpp/convert.py {MODEL_NAME} --outtype f16 --outfile {fp16}
```
- `fp16`: FP16 formatındaki model dosyasının yolu.
- `convert.py`: Modeli FP16 formatına dönüştürmek için kullanılan betik. Bu, GGUF nicemleme için bir ara adımdır.

#### 4. Nicemleme İşlemi
```python
METHOD = "q4_k_m"
qtype = f"{MODEL_NAME}/{MODEL_NAME.lower()}.{METHOD.upper()}.gguf"
!./llama.cpp/quantize {fp16} {qtype} {METHOD}
```
- `METHOD`: Kullanılacak nicemleme yöntemi (örneğin, Q4_K_M).
- `qtype`: Nicemlenmiş model dosyasının yolu ve adı.
- `quantize`: Belirtilen FP16 model dosyasını seçilen yöntemle nicemler.

#### 5. Nicemlenmiş Modelin Hugging Face Hub'a Yüklenmesi
```python
from huggingface_hub import create_repo, HfApi

hf_token = ""  # Hugging Face token
username = ""  # Hugging Face kullanıcı adı

api = HfApi()

# Boş depo oluşturma
create_repo(
    repo_id=f"{username}/{MODEL_NAME}-GGUF",
    repo_type="model",
    exist_ok=True,
    token=hf_token
)

# GGUF dosyalarını yükleme
api.upload_folder(
    folder_path=MODEL_NAME,
    repo_id=f"{username}/{MODEL_NAME}-GGUF",
    allow_patterns="*.gguf",
    token=hf_token
)
```
- `create_repo`: Hugging Face Hub'da model için yeni bir depo oluşturur.
- `upload_folder`: Nicemlenmiş model dosyalarını (`*.gguf`) depoya yükler.

Bu adımlar, bir modeli GGUF formatında nicemlemek ve Hugging Face Hub'a yüklemek için gereken işlemleri içerir.

---

