# GGUF, GPTQ ve EXL2 Dışındaki Quantization Teknikleri
GGUF, GPTQ ve EXL2 dışında çeşitli quantization teknikleri vardır. Bu bölüm, Activate-aware Weight Quantization (AWQ) (Aktive Farkında Ağırlık Quantization) ve QuIP# (Quantization with Incoherence Processing) (İnkoherens İşleme ile Quantization) ve HQQ (Half-Quadratic Quantization) (Yarı-Kuadratik Quantization) gibi aşırı quantization tekniklerini kısaca tanıtacaktır. 
Lin ve diğerleri (2023) tarafından tanıtılan AWQ, başka bir popüler quantization algoritmasıdır. 
Bu, ağırlık büyüklüğü yerine aktivasyon büyüklüğüne göre belirlenen en önemli ağırlıkları tanımlar ve korur. 
Bu yaklaşım, bu önemli ağırlıklara optimal kanal ölçeklendirmesi uygulayarak, geri yayılım veya yeniden yapılandırmaya güvenmeden, LLM'nin kalibrasyon setine aşırı uyum sağlamamasını sağlar. 
Farklı bir paradigmaya dayanmasına rağmen, AWQ, GPTQ ve EXL2 sürümlerine oldukça yakındır, ancak biraz daha yavaştır. 
Bunlar, çıkarım motorları tarafından iyi desteklenir ve TGI, vLLM ve TensorRT-LLM'ye entegre edilmiştir. 
İlginç bir trend, modellerin 1 veya 2 bit hassasiyetine quantize edilmesidir. 
EXL2 gibi bazı formatlar aşırı quantization'a izin verirken, modellerin kalitesi genellikle önemli ölçüde zarar görür. 
Ancak, QuIP# ve HQQ gibi son algoritmalar bu rejimi hedeflemiş ve orijinal modellerin performansını daha iyi koruyan quantization yöntemleri sunmuştur. 
Bu, özellikle büyük modeller (30B parametre üzerinde) için geçerlidir; bu modeller, 7B veya 13B parametre modellerinden daha az yer kaplayabilir ve daha yüksek kaliteli çıktılar sağlayabilir. 
Bu trendin devam etmesi ve bu quantization yöntemlerinin daha da optimize edilmesi beklenmektedir.

# Çıkarım Motorlarının Özelliklerinin Özeti
Bu bölümü sonlandırmak için, önceki bölümlerde ele aldığımız üç ana çıkarım motorunun özelliklerini özetleyen bir tabloyu burada bulabilirsiniz:
Teknik | TGI | vLLM | TensorRT-LLM 
---|:---:|:---:|:---:
Continuous batching (Sürekli toplu işleme) | ✓ | ✓ | ✓ 
Speculative decoding (Spekülatif kod çözme) |  | ✓ | 
FlashAttention2 | ✓ | ✓ | ✓ 
PagedAttention |  | ✓ | ✓ 
Pipeline parallelism (İşlem hattı paralelliği) |  | ✓ | 
Tensor parallelism (Tensor paralelliği) | ✓ | ✓ | ✓ 
GPTQ | ✓ | ✓ | 
EXL2 |  | ✓ | 
AWQ | ✓ | ✓ | ✓ 

Tablo 8.1 – TGI, vLLM ve TensorRT-LLM için özelliklerin özeti

### Kodların Ayrıntılı Açıklaması

Yukarıdaki metinde kod bulunmamaktadır. Ancak, tabloyu oluşturmak için kullanılan teknik terimlerin açıklamaları aşağıda verilmiştir:

- **Continuous batching (Sürekli toplu işleme)**: Çıkarım motorlarının, yeni girdi verileri geldiğinde, mevcut işlemi beklemeden yeni işlemi başlatabilme özelliğidir.
- **Speculative decoding (Spekülatif kod çözme)**: Çıkarım motorlarının, birden fazla olası çıktıdan en iyisini seçmeye çalışırken kullandığı bir tekniktir.
- **FlashAttention2**: Çıkarım motorlarında kullanılan, dikkat mekanizmasını hızlandırmak için tasarlanmış bir algoritmadır.
- **PagedAttention**: Çıkarım motorlarında kullanılan, dikkat mekanizmasını optimize etmek için tasarlanmış bir başka algoritmadır.
- **Pipeline parallelism (İşlem hattı paralelliği)**: Büyük modellerin, birden fazla işlemci veya GPU üzerinde paralel olarak işlenmesini sağlayan bir tekniktir.
- **Tensor parallelism (Tensor paralelliği)**: Büyük tensorlerin, birden fazla işlemci veya GPU üzerinde paralel olarak işlenmesini sağlayan bir tekniktir.
- **GPTQ, EXL2, AWQ**: Farklı quantization algoritmalarıdır. 
  - **GPTQ**: Yüksek doğruluklu quantization için kullanılan bir algoritmadır.
  - **EXL2**: Aşırı quantization için kullanılan bir formattır.
  - **AWQ (Activate-aware Weight Quantization)**: Ağırlık quantization'ı için kullanılan, aktivasyon büyüklüğüne dayanan bir algoritmadır.

---

