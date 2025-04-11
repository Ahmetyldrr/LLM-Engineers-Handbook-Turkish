#Yüksek Kaliteli Tercih Veri Kümeleri Oluşturma İlkeleri

Yüksek kaliteli tercih veri kümeleri (preference datasets) oluşturma ilkeleri, Bölüm 5'te talimat veri kümeleri (instruction datasets) için tartışılan ilkelerle aynıdır. Örneklerimizin doğruluk (accuracy), çeşitlilik (diversity) ve karmaşıklığını (complexity) en üst düzeye çıkarmak istiyoruz. Bunu başarmak için, Şekil 6.1'de özetlenen aynı aşamaları takip ediyoruz: veri kürasyon (data curation), yinelenenleri kaldırma (deduplication), bulaşıkları temizleme (decontamination), kalite değerlendirmesi (quality evaluation), keşif (exploration), oluşturma (generation) ve artırma (augmentation).

#Şekil 6.1 – Bu bölümde ele alınan eğitim sonrası veri ardışık düzeni (post-training data pipeline)

Tekrarı önlemek için, bu bölüm talimat ve tercih veri kümeleri arasındaki ana farklılıklara odaklanacaktır. Tercih örneklerinin (preference samples) yapısını ve tercih veri kümeleri için ideal boyutu tanıtacağız. Ardından, talimat veri kümeleri oluşturmaktan en çok farklı olan iki aşamaya odaklanacağız: veri oluşturma (data generation) ve değerlendirme (evaluation).

**Kodların Ayrıntılı Açıklaması:**

1. `The principles for creating high-quality preference datasets are the same as those discussed in Chapter 5 for instruction datasets.`
   - Yüksek kaliteli tercih veri kümeleri oluşturma ilkeleri, Bölüm 5'te tartışılan talimat veri kümeleri ilkeleriyle aynıdır.

2. `We want to maximize the accuracy, diversity, and complexity of our samples.`
   - Örneklerimizin doğruluk, çeşitlilik ve karmaşıklığını en üst düzeye çıkarmak istiyoruz.
   - İngilizce teknik terimler: accuracy (doğruluk), diversity (çeşitlilik), complexity (karmaşıklık)

3. `To achieve this, we follow the same stages, as outlined in Figure 6.1 : data curation, deduplication, decontamination, quality evaluation, exploration, generation, and augmentation.`
   - Bunu başarmak için, Şekil 6.1'de özetlenen aynı aşamaları takip ediyoruz: veri kürasyon, yinelenenleri kaldırma, bulaşıkları temizleme, kalite değerlendirmesi, keşif, oluşturma ve artırma.
   - İngilizce teknik terimler: data curation (veri kürasyon), deduplication (yinelenenleri kaldırma), decontamination (bulaşıkları temizleme), quality evaluation (kalite değerlendirmesi), exploration (keşif), generation (oluşturma), augmentation (artırma)

4. `Figure 6.1 – Overview of the post-training data pipeline covered in this chapter`
   - Şekil 6.1 – Bu bölümde ele alınan eğitim sonrası veri ardışık düzeni.
   - İngilizce teknik terim: post-training data pipeline (eğitim sonrası veri ardışık düzeni)

5. `To avoid repetition, this section will focus on the main differences between instruction and preference datasets.`
   - Tekrarı önlemek için, bu bölüm talimat ve tercih veri kümeleri arasındaki ana farklılıklara odaklanacaktır.

6. `We will introduce the structure of preference samples and the ideal size for preference datasets.`
   - Tercih örneklerinin yapısını ve tercih veri kümeleri için ideal boyutu tanıtacağız.
   - İngilizce teknik terimler: preference samples (tercih örnekleri), preference datasets (tercih veri kümeleri)

7. `Then, we will focus on the two stages that differ most from creating instruction datasets: data generation and evaluation.`
   - Ardından, talimat veri kümeleri oluşturmaktan en çok farklı olan iki aşamaya odaklanacağız: veri oluşturma ve değerlendirme.
   - İngilizce teknik terimler: data generation (veri oluşturma), evaluation (değerlendirme)

---

