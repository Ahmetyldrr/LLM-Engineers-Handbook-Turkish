# Quantization (Nicemleme)

Quantization (Nicemleme), bir sinir ağının (neural network) ağırlıklarını (weights) ve aktivasyonlarını (activations) daha düşük hassasiyetli (lower-precision) veri türleri kullanarak temsil etme sürecini ifade eder. LLMs (Büyük Dil Modelleri) bağlamında, quantization (nicemleme) öncelikle modelin ağırlıklarının ve aktivasyonlarının hassasiyetini azaltmaya odaklanır. Varsayılan olarak, ağırlıklar genellikle 16-bit veya 32-bit kayan noktalı (floating-point) formatta (FP16 veya FP32) depolanır, bu da yüksek hassasiyet sağlar ancak artan bellek kullanımı (memory usage) ve hesaplama karmaşıklığı (computational complexity) pahasına olur. Quantization (nicemleme), LLMs'in bellek ayak izini (memory footprint) azaltmak ve çıkarımını (inference) hızlandırmak için bir çözümdür. Bu faydaların yanı sıra, 30 milyardan fazla parametreye sahip daha büyük modeller, 2- veya 3-bit hassasiyete nicemlendiğinde daha küçük modellerden (7B–13B LLMs) kalite açısından daha iyi performans gösterebilir. Bu, karşılaştırılabilir bir bellek ayak izini korurken üstün performans elde edebilecekleri anlamına gelir.

Bu bölümde, quantization (nicemleme), GGUF ile llama.cpp, GPTQ ve EXL2 kavramlarını, ek tekniklere genel bir bakışla birlikte tanıtacağız. Bu bölümde sağlanan koda ek olarak, bir Google Colab not defterini kullanarak modellerini nicemlemek için AutoQuant (bit.ly/autoquant) adresine başvurabilirsiniz.

**Kod Açıklaması:**

Bu metinde kod bulunmamaktadır, ancak AutoQuant için verilen bağlantı bir kod örneği içermektedir.

Eğer kod olsaydı, ayrıntılı olarak satır satır kodun altında açıklanacaktı.

**İngilizce Teknik Terimlerin Türkçe Karşılıkları ve Parantez İçinde Eklenmesi:**

- Quantization (Nicemleme)
- Neural Network (Sinir Ağı)
- Weights (Ağırlıklar)
- Activations (Aktivasyonlar)
- Lower-precision data types (Düşük hassasiyetli veri türleri)
- LLMs (Büyük Dil Modelleri)
- Floating-point format (Kayan noktalı format)
- FP16 (16-bit kayan noktalı format)
- FP32 (32-bit kayan noktalı format)
- Memory usage (Bellek kullanımı)
- Computational complexity (Hesaplama karmaşıklığı)
- Memory footprint (Bellek ayak izi)
- Inference (Çıkarım)

**Kodların Birebir Yazılması:**

Bu metinde kod bulunmamaktadır, ancak eğer olsaydı, kodlar birebir aynısı olarak yazılacaktı.

---

