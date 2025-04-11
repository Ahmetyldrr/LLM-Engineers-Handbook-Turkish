# Ağırlık Kuantalama (Weight Quantization) Yaklaşımları
Ağırlık kuantalama (Weight Quantization) için iki ana yaklaşım vardır: Eğitim Sonrası Kuantalama (Post-Training Quantization, PTQ) ve Kuantalama Farkındalıklı Eğitim (Quantization-Aware Training, QAT). PTQ, önceden eğitilmiş bir modelin ağırlıklarının doğrudan daha düşük bir hassasiyet formatına dönüştürülmesiyle yapılan basit bir tekniktir ve herhangi bir yeniden eğitim gerektirmez. PTQ uygulamak kolay olsa da, bazı performans düşüşlerine neden olabilir. 
Tersine, QAT, eğitim veya ince ayar aşamasında kuantalama gerçekleştirir ve modelin daha düşük hassasiyetli ağırlıklara uyum sağlamasına olanak tanır. QAT, PTQ'ye kıyasla genellikle daha iyi performans sağlar, ancak ek hesaplama kaynakları ve temsili eğitim verileri gerektirir.

# Veri Türü Seçimi
Veri türü seçimi kuantalama işleminde kritik bir rol oynar. Derin öğrenmede genellikle FP32, FP16 (yarım hassasiyet) ve BF16 (beyin kayan nokta) gibi kayan noktalı sayılar kullanılır. Bu formatlar, bir sayının işaretini (sign), üssünü (exponent) ve anlamlı kısmını (significand veya mantissa) temsil etmek için sabit sayıda bit ayırır.

## FP32, FP16 ve BF16 Formatlarının Karşılaştırılması
Şekil 8.9 – FP32, FP16 ve BF16 formatlarının karşılaştırılması
0'ın işareti pozitif bir sayıyı temsil ederken, 1 negatif bir sayıyı gösterir. Tersine, üs, temsil edilen aralığı (büyük veya küçük) kontrol eder. Son olarak, anlamlı kısım sayının hassasiyetini (basamak sayısı) kontrol eder. Bu temsilleri gerçek sayılara dönüştürmek için kullanılan formül:
```
Gerçek Sayı = (-1)^işaret * 2^(üssü - bias) * (1 + anlamlı kısım)
```
Şekil 7.7'de gösterilen veri türleri, farklı temsillerle gösterilen farklı ödünleşimler sergiler (örneğin, ). FP32, 32 bit kullanarak yüksek hassasiyet sağlar, ancak daha fazla bellek gerektirir. Tersine, FP16 ve BF16, 16 bit kullanarak bellek ayak izini düşürür, ancak daha düşük hassasiyet sağlar. Genel olarak, sinir ağları daha iyi hassasiyet yerine daha büyük bir aralık tercih eder, bu nedenle donanım BF16'yı desteklediğinde en popüler veri türü olur. Örneğin, NVIDIA'nın Ampere mimarisi (A100, A30, vb.) BF16'yı destekler, ancak önceki nesiller gibi Turing (T4, T40, vb.) desteklemez.

# Düşük Hassasiyetli Veri Türleri
Ancak bu üç veri türüyle sınırlı değiliz. INT8 (8-bit tam sayılar) gibi daha düşük hassasiyetli veri türleri, kuantalama için kullanılabilir ve bellek ayak izini daha da azaltabilir. Naif kuantalama teknikleri, örneğin mutlak maksimum (absmax) kuantalama ve sıfır noktası kuantalama, FP32, FP16 veya BF16 ağırlıklarını INT8'e dönüştürmek için uygulanabilir, Şekil 8.10'da gösterildiği gibi:
Şekil 8.10 – Absmax kuantalama ve sıfır noktası kuantalama ile 0.1'in kuantalama işlemi

## Absmax Kuantalama
Absmax kuantalama, orijinal ağırlıkları [-127, 127] aralığına eşler, bunları mutlak maksimum değere bölerek ölçeklendirir:
```
X_quant = (127 / |X|_max) * X
```
Örneğin, eğer mutlak maksimum değerimiz 3.2 ise (Şekil 8.8), 0.1 ağırlığı `round((127/3.2)*0.1)` = 4'e kuantalanır. 
De-kuantalama için ters işlemi yaparız:
```
X_dequant = (3.2 / 127) * X_quant
```
Bu, 0.1 ağırlığımızı de-kuantaladığımızda `3.2/127*4` = 0.10079 elde ettiğimiz anlamına gelir. Bu örnekte yuvarlama hatası `0.1 - 0.10079` = -0.00079'dir.

### Python'da Absmax Kuantalama Uygulaması
```python
import torch

def absmax_quantize(X):
    # Ölçek hesapla
    scale = 127 / torch.max(torch.abs(X))
    # Kuantalama
    X_quant = (scale * X).round()
    return X_quant.to(torch.int8)
```

## Sıfır Noktası Kuantalama
Sıfır noktası kuantalama, asimetrik girdi dağılımlarını dikkate alır ve ağırlıkları [-128, 127] aralığına eşler, sıfır noktası ofseti tanıtarak:
```
X_quant = round((X - min_X) / (max_X - min_X) * 255) - 128
```
Burada `scale = 255 / (max_X - min_X)` ve `zeropoint = -scale * min_X - 128`.

### Python'da Sıfır Noktası Kuantalama Uygulaması
```python
def zeropoint_quantize(X):
    # Değer aralığını hesapla (payda)
    x_range = torch.max(X) - torch.min(X)
    x_range = 1 if x_range == 0 else x_range
    # Ölçek hesapla
    scale = 255 / x_range
    # Sıfır noktasına kaydır
    zeropoint = (-scale * torch.min(X) - 128).round()
    # Girdileri ölçeklendir ve yuvarla
    X_quant = torch.clip((X * scale + zeropoint).round(), -128, 127)
    return X_quant.to(torch.int8)
```

# Gelişmiş Kuantalama Teknikleri
Naif kuantalama yöntemlerinin sınırlamaları vardır, özellikle büyük dil modellerinde (LLM) aykırı özelliklerle uğraşırken. Aykırı özellikler, kuantalama işlemini önemli ölçüde etkileyebilen ve diğer değerler için azaltılmış hassasiyete yol açabilen aşırı ağırlık değerleridir (%0.1 toplam değer). Bu aykırı değerleri atmak mümkün değildir, çünkü modelin performansını düşürür.

## LLM.int8() Tekniği
Dettmers ve diğerleri (2022) tarafından tanıtılan LLM.int8(), karma hassasiyetli kuantalama şeması kullanır; burada aykırı özellikler FP16 kullanılarak işlenirken, kalan değerler INT8'e kuantalanır. Bu yaklaşım, LLM'lerin bellek ayak izini neredeyse 2 kat azaltırken performans bozulmasını en aza indirir.

### LLM.int8() Uygulaması
LLM.int8(), matris çarpımını üç adımda gerçekleştirir. İlk olarak, girdi gizli durumlarından aykırı özellik içeren sütunları özel bir eşik kullanarak ayıklar. İkinci olarak, aykırı değerler (FP16'da) ve aykırı olmayan değerler (INT8'de) için ayrı matris çarpımları gerçekleştirir. Son olarak, aykırı olmayan sonuçların de-kuantalanmasını sağlar ve bunları aykırı sonuçlarla birleştirerek FP16'da nihai çıktıyı elde eder.

### Transformer Kütüphanesi ile 8-Bit Hassasiyetli Model Yükleme
```python
from transformers import AutoModelForCausalLM

model_name = "meta-llama/Meta-Llama-3-8B-Instruct"
model = AutoModelForCausalLM.from_pretrained(model_name, device_map="auto", load_in_8bit=True)
```

## NF4 Tekniği
Dettmers ve diğerleri (2023) tarafından tanıtılan NF4, QLoRA için tasarlanmış 4-bit hassasiyet formatıdır. Transformer kütüphanesine entegre edilmiştir, ancak bitsandbytes kütüphanesini bağımlılık olarak gerektirir.

### NF4 ile 4-Bit Hassasiyetli Model Yükleme
```python
from transformers import AutoModelForCausalLM

model_name = "meta-llama/Meta-Llama-3-8B-Instruct"
model = AutoModelForCausalLM.from_pretrained(model_name, device_map="auto", load_in_4bit=True)
```

---

