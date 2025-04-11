#İncelenen Konu: İnce Ayar (Fine-Tuning) Gerekliliği

Çoğu senaryoda, doğrudan ince ayar (fine-tuning) yerine hızlı mühendislik (prompt engineering) ile başlamak önerilir. Hızlı mühendislik, açık ağırlıklı (open-weight) veya kapalı kaynaklı (closed-source) modellerle kullanılabilir. Few-shot prompting veya retrieval augmented generation (RAG) gibi teknikler kullanılarak, birçok sorun Supervised Fine-Tuning (SFT) olmadan verimli bir şekilde çözülebilir. Hızlı mühendislik, aynı zamanda doğruluk (accuracy), maliyet (cost) ve gecikme (latency) gibi ölçümleri ölçen sağlam bir değerlendirme hattı (evaluation pipeline) oluşturmamıza da olanak tanır. Bu sonuçlar gereksinimlerle eşleşmezse, önceki bölümde gösterildiği gibi bir talimat veri kümesi (instruction dataset) oluşturma olasılığını keşfedebiliriz. Yeterli veri mevcutsa, ince ayar bir seçenek haline gelir.

#Şekil 5.8 – Teknik Seviyede İnce Ayarın Bir Seçenek Olup Olmadığını Belirlemek için Temel Akış Şeması

Bu teknik değerlendirmelerin ötesinde, SFT, kontrol ("verilerinizi bilin") ve özelleştirme (fine-tuned model benzersizdir) açısından ortak ihtiyaçları karşılar. Bir sohbet robotu etrafında uygulamalar oluşturmak yerine, ince ayar, geliştiricilerin araç analitiği (tool analytics), moderasyon (moderation) ve ek içerik (additional context) gibi LLM'lerle daha çeşitli etkileşimler oluşturmasına olanak tanır. Bu kitapta açık ağırlıklı modellere odaklanmamıza rağmen, birçok LLM sağlayıcısı otomatik ince ayar hizmetleri sunmaktadır. Kendi ince ayar hattınızı yönetmekle aynı düzeyde kontrol ve özelleştirme sunmasalar da, belirli senaryolarda (örneğin, makine öğrenimi mühendisliği açısından sınırlı kaynaklar) ilginç bir değiş tokuş olabilir.

Bu avantajlara rağmen, ince ayarın da sınırlamaları vardır. SFT'nin, temel modelin ağırlıklarındaki (weights) önceden var olan bilgiyi kullandığı ve parametreleri belirli bir amaç için yeniden odakladığı genel olarak kabul edilmektedir. Bunun birkaç anlamı vardır. Her şeyden önce, ön eğitim kümesinde (pre-training set) öğrenilenlerden çok uzak olan bilgi (örneğin, bilinmeyen veya nadir bir dil) etkili bir şekilde öğrenilmesi zor olabilir. Daha da kötüsü, bir çalışma, bir modeli yeni bilgi üzerinde ince ayarın daha sık hallüsinasyonlara (hallucinations) yol açabileceğini göstermiştir. Kullanılan SFT tekniğine bağlı olarak, temel modelde mevcut olan bilginin silinmesi riski de vardır (yaygın bir sorun olarak "felaketli unutma" (catastrophic forgetting) olarak adlandırılır).

**Kod Açıklaması**

Bu metinde kod bulunmamaktadır. Ancak, metinde geçen teknik terimlerin açıklamaları aşağıda verilmiştir:

- **Few-shot prompting**: Bir modelin az sayıda örnek ile eğitilmesi veya yönlendirilmesi tekniğidir.
- **Retrieval Augmented Generation (RAG)**: Metin oluşturma görevlerinde, ilgili bilgilerin bir veri tabanından veya başka bir kaynaktan alınarak modele eklenmesi tekniğidir.
- **Supervised Fine-Tuning (SFT)**: Bir modelin önceden eğitildikten sonra, belirli bir görev için etiketli verilerle ince ayarının yapılmasıdır.
- **Evaluation pipeline**: Bir modelin performansını değerlendirmek için kullanılan ardışık işlemler zinciridir.
- **Accuracy**: Bir modelin doğru tahminlerinin oranıdır.
- **Cost**: Bir modelin çalıştırılması veya eğitilmesi için gereken kaynakların (hesaplama gücü, zaman, vb.) maliyetidir.
- **Latency**: Bir modelin bir isteğe cevap vermesi için geçen süredir.
- **Instruction dataset**: Bir modelin belirli görevleri yerine getirmesi için eğitildiği veri kümesidir.
- **Open-weight models**: Ağırlıkları açıkça erişilebilen modellerdir.
- **Closed-source models**: Kaynak kodu veya ağırlıkları gizli olan modellerdir.
- **Hallucinations**: Bir modelin gerçekte olmayan veya doğru olmayan bilgiler üretmesidir.
- **Catastrophic forgetting**: Bir modelin yeni bilgiler öğrenirken eski bilgilerini unutmasıdır.

---

