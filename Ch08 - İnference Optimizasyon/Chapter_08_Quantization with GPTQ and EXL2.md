# GGUF ve Llama.cpp ile GPTQ ve EXL2 Karşılaştırması

GGUF ve llama.cpp CPU'da GPU offloading (GPU boşaltma) ile çıkarım (inference) sunarken, GPTQ ve EXL2 GPU'lara özel iki nicelendirme (quantization) formatıdır. Bu onları çıkarım sırasında llama.cpp'den daha hızlı yapar. Özellikle, EXL2, özel kitaplığı ExLlamaV2 ile en yüksek verimi (throughput) sunar. GPTQ ve EXL2 nicelendirmeleri, Frantar ve diğerleri (2023) tarafından tanıtılan GPTQ algoritmasına dayanır. Bu algoritma, Optimal Brain Quantization (OBQ) yaklaşımını geniş matrisleri verimli bir şekilde işleyecek şekilde geliştirerek LLMs için ağırlık nicelendirmesini optimize eder. Hessian inverse'nin Cholesky ayrıştırmasıyla başlar ve sayısal kararlılığı sağlar. Ağırlıkları katı bir sırayla nicelendirmek yerine, GPTQ bunları partiler halinde işler, sütunları ve ilişkili blokları yinelemeli olarak günceller. Bu yöntem, tembel parti güncellemelerinden (lazy batch updates) yararlanarak hesaplama fazlalığını ve bellek darboğazlarını azaltır. GPTQ 4-bit hassasiyete (precision) sınırlı iken, EXL2 farklı nicelendirme seviyelerini karıştırabilen yüksek oranda özelleştirilebilir bir hassasiyet sunar. Bu, ağırlık başına 2 ila 8 bit arasında kesin bit oranlarına (bitrates) izin verir, örneğin 2.3, 3.5 veya 6.0. Her bir doğrusal katmana (linear layer) birden fazla nicelendirme seviyesi uygulayabilir, daha önemli ağırlıkları daha yüksek bit nicelendirmesiyle önceliklendirebilir. Parametreler otomatik olarak seçilir, her bir matrisi birden çok kez nicelendirerek ve hedef bit oranını karşılarken nicelendirme hatasını en aza indiren bir kombinasyon seçerek. Pratikte bu, 70B modellerin tek bir 24 GB GPU'da 2.55-bit hassasiyetle çalışmasına izin verir. Çıkarımın kendisi, hem GPTQ hem de EXL2 modellerini destekleyen ExLlamaV2 kitaplığı tarafından işlenir.

Aşağıdaki örnekte, ExLlamaV2 kullanarak EXL2 formatında bir modeli nicelendirelim. Bu adımlar Google Colab'da ücretsiz bir T4 GPU'da çalıştırılabilir:

ExLlamaV2 kitaplığını kaynaktan yükleyin:
```bash
!git clone https://github.com/turboderp/exllamav2
!pip install -e exllamav2
```
*   `!git clone https://github.com/turboderp/exllamav2`: ExLlamaV2 deposunu klonlar.
*   `!pip install -e exllamav2`: ExLlamaV2'yi yükler.

Nicelendirmek için modeli Hugging Face Hub'dan klonlayarak indiriyoruz:
```python
MODEL_ID = "meta-llama/Llama-2-7b-chat-hf"
MODEL_NAME = MODEL_ID.split('/')[-1]
!git lfs install
!git clone https://huggingface.co/{MODEL_ID}
```
*   `MODEL_ID = "meta-llama/Llama-2-7b-chat-hf"`: Model kimliğini belirler.
*   `MODEL_NAME = MODEL_ID.split('/')[-1]`: Model adını belirler.
*   `!git lfs install`: Git LFS'i yükler.
*   `!git clone https://huggingface.co/{MODEL_ID}`: Modeli Hugging Face Hub'dan klonlar.

Nicelendirme hatasını ölçmek için kullanılan kalibrasyon veri setini indirin. Bu durumda, Wikipedia'dan yüksek kaliteli makalelere sahip standart bir kalibrasyon veri seti olan WikiText-103'ü kullanacağız:
```bash
!wget https://huggingface.co/datasets/wikitext/resolve/9a9e482b5987f9d25b3a9b2883fc6cc9fd8071b3/wikitext-103-v1/wikitext-test.parquet
```
*   `!wget https://huggingface.co/datasets/wikitext/resolve/9a9e482b5987f9d25b3a9b2883fc6cc9fd8071b3/wikitext-103-v1/wikitext-test.parquet`: WikiText-103 test veri setini indirir.

Modeli belirli bir hassasiyette (örneğin, 4.5) nicelendirin:
```bash
!mkdir quant
!python exllamav2/convert.py \
    -i {MODEL_NAME} \
    -o quant \
    -c wikitext-test.parquet \
    -b 4.5
```
*   `!mkdir quant`: `quant` adlı bir dizin oluşturur.
*   `!python exllamav2/convert.py`: ExLlamaV2'nin `convert.py` betiğini çalıştırır.
*   `-i {MODEL_NAME}`: Giriş modeli belirler.
*   `-o quant`: Çıktı dizinini belirler.
*   `-c wikitext-test.parquet`: Kalibrasyon veri setini belirler.
*   `-b 4.5`: Hedef bit oranını belirler.

Nicelendirilmiş model daha sonra Hugging Face Hub'a yüklenebilir.

GPTQ ve EXL2 nicelendirmeleri GGUF kadar yaygın olarak desteklenmez. Örneğin, LM Studio gibi ön uçlar (frontends) şu anda bunları entegre etmez. Bunun yerine oobabooga'nın Text Generation Web UI gibi diğer araçları kullanabilirsiniz. Ayrıca doğrudan transformers kitaplığına entegre edilmiştir ve TGI tarafından desteklenir. GPTQ modelleri ayrıca TensorRT-LLM'de desteklenir. GGUF'den daha az popüler olsa da, Hugging Face Hub'da birçok GPTQ ve EXL2 modeli bulabilirsiniz.

# İngilizce Teknik Terimlerin Türkçeleri ve Açıklamaları

*   **Quantization**: Nicelendirme, bir modelin ağırlıklarını daha düşük hassasiyetli sayılara dönüştürme işlemidir.
*   **Inference**: Çıkarım, bir eğitilmiş modelin girdi verilerine dayanarak tahminler yapması işlemidir.
*   **GPU Offloading**: GPU Boşaltma, bir hesaplama görevinin bir kısmını GPU'ya aktararak CPU'nun iş yükünü azaltma işlemidir.
*   **Throughput**: Verim, bir sistemin birim zamanda işleyebildiği veri miktarını ifade eder.
*   **Optimal Brain Quantization (OBQ)**: Optimal Beyin Nicelendirmesi, sinir ağlarının nicelendirilmesi için bir yaklaşımdır.
*   **Cholesky Decomposition**: Cholesky Ayrıştırması, bir pozitif tanımlı matrisin özel bir şekilde ayrıştırılması işlemidir.
*   **Lazy Batch Updates**: Tembel Parti Güncellemeleri, birden fazla işlemi bir arada gruplayarak güncelleme işlemini optimize etme yöntemidir.
*   **Precision**: Hassasiyet, sayısal hesaplamalarda kullanılan sayıların kesinliğini ifade eder.
*   **Bitrate**: Bit Oranı, bir veri akışının birim zamanda taşıdığı bit miktarını ifade eder.
*   **Linear Layer**: Doğrusal Katman, sinir ağlarında doğrusal dönüşümler uygulayan katmandır.
*   **Calibration Dataset**: Kalibrasyon Veri Seti, bir modelin nicelendirme hatasını ölçmek için kullanılan veri setidir.

İngilizce paragrafın birebir Türkçe çevirisi:

GGUF ve llama.cpp CPU'da GPU offloading (GPU boşaltma) ile çıkarım (inference) sunarken, GPTQ ve EXL2 GPU'lara özel iki nicelendirme (quantization) formatıdır. Bu onları çıkarım sırasında llama.cpp'den daha hızlı yapar. Özellikle, EXL2, özel kitaplığı ExLlamaV2 ile en yüksek verimi (throughput) sunar. GPTQ ve EXL2 nicelendirmeleri, Frantar ve diğerleri (2023) tarafından tanıtılan GPTQ algoritmasına dayanır. Bu algoritma, Optimal Brain Quantization (OBQ) yaklaşımını geniş matrisleri verimli bir şekilde işleyecek şekilde geliştirerek LLMs için ağırlık nicelendirmesini optimize eder. Hessian inverse'nin Cholesky ayrıştırmasıyla başlar ve sayısal kararlılığı sağlar. Ağırlıkları katı bir sırayla nicelendirmek yerine, GPTQ bunları partiler halinde işler, sütunları ve ilişkili blokları yinelemeli olarak günceller. Bu yöntem, tembel parti güncellemelerinden (lazy batch updates) yararlanarak hesaplama fazlalığını ve bellek darboğazlarını azaltır. GPTQ 4-bit hassasiyete (precision) sınırlı iken, EXL2 farklı nicelendirme seviyelerini karıştırabilen yüksek oranda özelleştirilebilir bir hassasiyet sunar. Bu, ağırlık başına 2 ila 8 bit arasında kesin bit oranlarına (bitrates) izin verir, örneğin 2.3, 3.5 veya 6.0. Her bir doğrusal katmana (linear layer) birden fazla nicelendirme seviyesi uygulayabilir, daha önemli ağırlıkları daha yüksek bit nicelendirmesiyle önceliklendirebilir. Parametreler otomatik olarak seçilir, her bir matrisi birden çok kez nicelendirerek ve hedef bit oranını karşılarken nicelendirme hatasını en aza indiren bir kombinasyon seçerek. Pratikte bu, 70B modellerin tek bir 24 GB GPU'da 2.55-bit hassasiyetle çalışmasına izin verir. Çıkarımın kendisi, hem GPTQ hem de EXL2 modellerini destekleyen ExLlamaV2 kitaplığı tarafından işlenir.

Aşağıdaki örnekte, ExLlamaV2 kullanarak EXL2 formatında bir modeli nicelendirelim. Bu adımlar Google Colab'da ücretsiz bir T4 GPU'da çalıştırılabilir:

ExLlamaV2 kitaplığını kaynaktan yükleyin:
```bash
!git clone https://github.com/turboderp/exllamav2
!pip install -e exllamav2
```
Nicelendirmek için modeli Hugging Face Hub'dan klonlayarak indiriyoruz:
```python
MODEL_ID = "meta-llama/Llama-2-7b-chat-hf"
MODEL_NAME = MODEL_ID.split('/')[-1]
!git lfs install
!git clone https://huggingface.co/{MODEL_ID}
```
Nicelendirme hatasını ölçmek için kullanılan kalibrasyon veri setini indirin. Bu durumda, Wikipedia'dan yüksek kaliteli makalelere sahip standart bir kalibrasyon veri seti olan WikiText-103'ü kullanacağız:
```bash
!wget https://huggingface.co/datasets/wikitext/resolve/9a9e482b5987f9d25b3a9b2883fc6cc9fd8071b3/wikitext-103-v1/wikitext-test.parquet
```
Modeli belirli bir hassasiyette (örneğin, 4.5) nicelendirin:
```bash
!mkdir quant
!python exllamav2/convert.py \
    -i {MODEL_NAME} \
    -o quant \
    -c wikitext-test.parquet \
    -b 4.5
```
Nicelendirilmiş model daha sonra Hugging Face Hub'a yüklenebilir.

GPTQ ve EXL2 nicelendirmeleri GGUF kadar yaygın olarak desteklenmez. Örneğin, LM Studio gibi ön uçlar (frontends) şu anda bunları entegre etmez. Bunun yerine oobabooga'nın Text Generation Web UI gibi diğer araçları kullanabilirsiniz. Ayrıca doğrudan transformers kitaplığına entegre edilmiştir ve TGI tarafından desteklenir. GPTQ modelleri ayrıca TensorRT-LLM'de desteklenir. GGUF'den daha az popüler olsa da, Hugging Face Hub'da birçok GPTQ ve EXL2 modeli bulabilirsiniz.

---

