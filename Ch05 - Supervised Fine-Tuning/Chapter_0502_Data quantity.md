#Hugging Face Hub'da İnstruction Datasetleri (Instruction Datasets on the Hugging Face Hub)

Hugging Face Hub, genel amaçlı veya belirli görevler veya alanlar için tasarlanmış çok sayıda instruction dataseti (instruction datasets) içerir. Yeni bir kullanım durumu üzerinde çalışırken, ince ayar (fine-tuning) için yararlanmak üzere ilgili açık kaynaklı datasetleri aramak faydalı olabilir. Bu, özellikle örnek sayınız çok düşük olduğunda (örneğin, 1.000'den az), yüksek kaliteli verilerle arttırmanızı gerektirdiğinde önemlidir. 
#Figure 5.2 – Hugging Face Hub'daki en beğenilen datasetlerin ekran görüntüsü (Screenshot of the most-liked datasets on the Hugging Face Hub)

İdeal örnek sayısını hesaplamak zor bir iştir, çünkü hem verilerin kalitesi hem de modelin boyutu dramatik bir etkiye sahip olabilir. Büyük modeller (örneğin, yaklaşık 70 milyar parametre), bu sayı 1.000 yüksek kaliteli örnek kadar düşük olabilir (Referanslar bölümündeki LIMA makalesine bakınız). Bu, daha küçük modeller (örneğin, yaklaşık yedi milyar parametre) için geçerli değildir, çünkü doğru sohbet şablonunu (chat template) öğrenmek için daha fazla örneğe ihtiyaç duyarlar. Her durumda, verilerin kalitesi çok önemli bir faktördür ve yüksek sayıda örnek her zaman arzu edilir.

Ek sayılar sağlamak için, şirketler ve açık kaynak topluluğu tarafından geliştirilen ince ayarlı modellere (fine-tuned models) bakabiliriz. İki tür ince ayar ayırt edebiliriz: genel amaçlı, GPT gibi modellerin yeteneklerini yeniden üretmeyi amaçlayan ve görev veya alan spesifik modeller, belirli bir uygulama için performanslarını optimize etmek üzere tasarlanmıştır. Genel amaçlı modeller daha fazla konu kapsar, bu da ek örnekler gerektirir.

Şirketler arasında geniş bir değer aralığı gözlemliyoruz. Örneğin, 01-ai'den Yi modelleri 10.000'den az örneğe dayanıyor. Spektrumun karşı ucunda, Meta, tüm ince ayar süreci boyunca (tercih hizalaması dahil) Llama 3 için 10 milyon örnek kullandığını bildirdi. Açık kaynak topluluğunda, OpenHermes ve Dolphin gibi modeller yaklaşık bir milyon örnek kullanıyor. Bu ince ayarların kalitesine dayanarak, iyi bir genel amaçlı instruct modeli oluşturmak için en az bir milyon örnek içeren bir instruction dataseti öneriyoruz.

Öte yandan, belirli bir amaç için ince ayarlı modeller daha az örneğe ihtiyaç duyar. Burada, görev spesifik modelleri alan spesifik modellerden ayırıyoruz. Görev spesifik modelleri ve alan spesifik modelleri, LLMs ince ayarı için iki farklı yaklaşımı temsil eder.

Görev spesifik modelleri, çeviri, özetleme veya duygu analizi gibi belirli bir işlevde mükemmelleşmek üzere tasarlanmıştır. Bu modeller, tek bir görev üzerinde odaklanmış bir eğitim yaklaşından yararlanır ve daha küçük model boyutlarıyla (genellikle 8 milyardan az parametre) bile verimli performans sağlar. Görev spesifik ince ayar için gereken veriler genellikle daha yönetilebilir durumdadır ve 100 ila 100.000 örnek arasında değişir. Bu, görev spesifik ince ayarı birçok uygulama için çekici bir seçenek haline getirir.

Alan spesifik modelleri, diğer yandan, belirli bir alanın söz varlığı ve dil kalıplarıyla tanışıklık ve uzmanlık bilgisi ile LLMs'i ayarlamayı amaçlar. Bu modeller, tıp, hukuk, finans, e-ticaret, mühendislik ve konukseverlik gibi alanlarda değerlidir. Alan spesifik ince ayar için veri gereksinimleri, alanın karmaşıklığına ve genişliğine bağlı olarak büyük ölçüde değişebilir. Tıp veya hukuk gibi bazı alanlar, genel amaçlı ince ayarda olduğu gibi çok fazla veriye ihtiyaç duyabilir. E-ticaret veya konukseverlik gibi diğerleri, görev spesifik ince ayarı ile daha uyumlu olarak daha az örneğe ihtiyaç duyabilir.

Alan spesifik modeller için veri ihtiyaçlarını belirleyen anahtar faktörler, alanın “boyutu” (yani, uzmanlık bilgisi ve söz varlığının kapsamı) ve modelin ön eğitim verilerinde bu alanın temsil edilmesidir. Orijinal eğitim verilerinde iyi temsil edilen alanlar daha az ince ayara ihtiyaç duyabilirken, daha özel veya az temsil edilenler daha kapsamlı datasetlere ihtiyaç duyabilir.

Açık kaynaklı LLMs'lerle bile, birçok ön eğitim dataseti kapalı kaynaktır, bu da bileşimleri hakkında eğitimli tahminler yapmayı gerektirir (örneğin, %30 kod veya %20 matematik).

**Kod Açıklaması:**

Bu metinde kod bulunmamaktadır. Ancak, metinde geçen bazı teknik terimlerin açıklamaları aşağıda verilmiştir:

- **Instruction datasets**: Modelin belirli görevleri yerine getirmesini sağlamak için kullanılan, talimatlar ve bu talimatlara karşılık gelen cevapları içeren datasetlerdir.
- **Fine-tuning**: Önceden eğitilmiş bir modelin, belirli bir görev veya alan için daha iyi performans göstermesi amacıyla, daha küçük bir dataset üzerinde yeniden eğitilmesidir.
- **LLMs (Large Language Models)**: Büyük miktarda metin verisi üzerinde eğitilen ve dilin yapısını öğrenen modellerdir.
- **GPT**: OpenAI tarafından geliştirilen, büyük bir dil modeli olan Generative Pre-trained Transformer'ın kısaltmasıdır.
- **Parametre**: Modelin eğitilebilir öğeleridir ve modelin karmaşıklığını belirler.
- **Chat template**: Modelin sohbet şeklinde girdi almasını ve buna karşılık vermesini sağlayan şablondur.
- **Task-specific fine-tuning**: Modelin belirli bir görev için (örneğin, çeviri, özetleme) ince ayar yapılmasıdır.
- **Domain-specific fine-tuning**: Modelin belirli bir alan (örneğin, tıp, hukuk) için ince ayar yapılmasıdır.

---

