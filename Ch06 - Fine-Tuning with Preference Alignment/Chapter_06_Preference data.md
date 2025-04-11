# Tercih Veri Kümeleri (Preference Datasets)

Tercih veri kümeleri, farklı eğitim algoritmaları arasındaki değişen veri gereksinimleri nedeniyle talimat veri kümelerinin (instruction datasets) standartlaştırılmasından yoksundur. Tercih verileri, insan veya dil modelleri tarafından sıralanan belirli bir talimat için yanıtların bir koleksiyonunu içerir. Bu bölüm DPO'ya odaklanmaktadır, bu nedenle bu algoritma tarafından gereken özel veri formatını inceleyeceğiz. Tablo 6.1'de gösterildiği gibi, DPO veri kümelerinin yapısı basit: her talimat, bir tercih edilen yanıt (chosen answer) ve bir reddedilen yanıt (rejected answer) ile eşleştirilir. Amaç, modeli reddedilen yanıt yerine tercih edilen yanıtı üretmesi için eğitmekdir.

Talimat (Instruction) : Bana ahtapotlar hakkında bir şaka söyle.
Tercih Edilen Yanıt (Chosen answer) : Ahtapotlar kumarhanelerde neden kağıt oynamazlar? Çünkü sekizden fazlasını sayamazlar.
Reddedilen Yanıt (Rejected answer) : Bir ahtapotu güldürmek kaç tane gıdıklamaya neden olur? On gıdıklama.

Tablo 6.1 – mlabonne/orpo-dpo-mix-40k veri kümesinden örnek

Tercih veri kümelerinde, reddedilen yanıt (rejected response), tercih edilen yanıt kadar önemlidir. Reddedilen yanıt olmadan, veri kümesi basit bir talimat kümesi olurdu. Reddedilen yanıtlar, modelden çıkarmak istediğimiz davranışı temsil eder. Bu, çok fazla esneklik sağlar ve tercih veri kümelerini birçok bağlamda kullanmamıza olanak tanır.

Aşağıda, SFT (Supervised Fine-Tuning) tek başına kullanmaya kıyasla tercih veri kümelerinin daha faydalı olduğu örneklerin bir listesi bulunmaktadır:

*   **Chatbotlar (Chatbots)** : Konuşmalı AI'da (Conversational AI), yanıtların kalitesi genellikle doğallık, etkileşim ve bağlamsal uygunluk gibi öznel faktörlere bağlıdır. Tercih veri kümesi, modeli daha iyi ve daha kötü yanıtları karşılaştırarak bu incelikleri öğrenmeye olanak tanır. Basit SFT, belirli bir bağlamda bir yanıtın diğerinden daha tercih edilir olmasını sağlayan incelikleri yakalayamayabilir.
*   **İçerik Moderasyonu (Content Moderation)** : İçeriğin uygun olup olmadığını veya yönergeleri ihlal edip etmediğini belirlemek genellikle incelikli yargıları içerir. Tercih veri kümeleri, modele kabul edilebilir ve kabul edilemez içerik örneklerini karşılaştırarak sınırdaki durumları ayırt etmeyi öğrenmede yardımcı olabilir. Bu, SFT yoluyla ikili sınıflandırmadan daha etkilidir, çünkü modele moderasyon kararlarının arkasındaki mantığı anlamada yardımcı olur.
*   **Özetleme (Summarization)** : Bir özetin kalitesi genellikle kısalık, alaka ve tutarlılık gibi faktörlere bağlıdır. Tercih veri kümeleri kullanarak, modeller daha yararlı ve bilgilendirici özetler üretebilir. Basit SFT, teknik olarak doğru ancak insan okuyucular için daha az tercih edilen özetlerle sonuçlanabilir.
*   **Kod Üretimi (Code Generation)** : Kodlama görevlerinde, genellikle birden fazla doğru çözüm vardır, ancak bazıları daha verimli veya okunabilir veya daha iyi uygulamalara uyar. Tercih veri kümeleri, modele basit doğruluk tabanlı SFT tarafından yakalanmayabilecek kod kalitesinin nitel yönlerini öğrenmede yardımcı olabilir.
*   **Yaratıcı Yazma (Creative Writing)** : Hikaye oluşturma veya şiir yazma gibi görevler için, çıktının kalitesi oldukça öznel ve çok yönlüdür. Tercih veri kümeleri, stil, yaratıcılık ve duygusal etki hakkında insan yargılarını yakalayabilir, teknik doğruluğa veya talimatlara uymaya daha fazla odaklanabilecek talimat veri kümelerinden daha iyi olabilir.
*   **Çeviri (Translation)** : Geleneksel ölçütler (metrics) gibi BLEU skorları çeviri doğruluğunu ölçebilir, ancak her zaman çevirinin akıcılığını veya doğallığını yakalayamaz. Tercih veri kümeleri, modele teknik olarak doğru olan birden fazla çeviri olduğunda bile yerel konuşmacıların tercih ettiği çevirileri üretmeyi öğrenmede yardımcı olabilir.

Tüm bu senaryolarda, tercih veri kümeleri daha rafine bir eğitim yaklaşımı sağlar. Basit doğruluğun veya talimatlara uymanın ötesinde, öznel kalite değerlendirmelerini ve insan tercihlerini yakalar. Bu yöntem, yalnızca teknik olarak doğru değil, aynı zamanda karmaşık, açık uçlu görevlerde insan yargısı ve tercihleriyle daha iyi hizalanmış çıktı üreten modeller üretebilir.

Talimat veri kümelerinden farklı olarak, Alpaca veya ShareGPT gibi standartlaştırılmış depolama formatları yoktur. Çoğu tercih veri kümesi, Tablo 6.1'de gösterilene benzer bir yapı izler ve bir talimat, bir tercih edilen yanıt ve bir reddedilen yanıt için sütunlar içerir. Çoklu dönüşlü (multi-turn) konuşmalar tercih uyumunda (preference alignment) yaygın değildir. Yazının yazıldığı sırada, büyük fine-tuning kütüphaneleri çoklu dönüşlü konuşmaları desteklemez ve genellikle bir sohbette yalnızca ilk veya son mesajı çıkarır.

Kodlar ve Açıklamaları:

DPO veri kümesinin yapısını anlamak için aşağıdaki kod örneğini inceleyelim:

```python
# Örnek veri kümesi
veri_kumesi = [
    {
        "talimat": "Bana ahtapotlar hakkında bir şaka söyle.",
        "tercih_edilen_yanit": "Ahtapotlar kumarhanelerde neden kağıt oynamazlar? Çünkü sekizden fazlasını sayamazlar.",
        "reddedilen_yanit": "Bir ahtapotu güldürmek kaç tane gıdıklamaya neden olur? On gıdıklama."
    }
]

# Veri kümesini yazdırma
for ornek in veri_kumesi:
    print("Talimat:", ornek["talimat"])
    print("Tercih Edilen Yanıt:", ornek["tercih_edilen_yanit"])
    print("Reddedilen Yanıt:", ornek["reddedilen_yanit"])
```

Bu kod, bir DPO veri kümesinin basit bir temsilini gösterir. Her örnek, bir talimat, bir tercih edilen yanıt ve bir reddedilen yanıttan oluşur.

1.  `veri_kumesi` adlı bir liste tanımlanır ve bu liste, her biri bir talimat, bir tercih edilen yanıt ve bir reddedilen yanıt içeren sözlükleri içerir.
2.  `for` döngüsü, `veri_kumesi` listesini yineler ve her örnek için talimat, tercih edilen yanıt ve reddedilen yanıtı yazdırır.

Bu kod, DPO veri kümesinin yapısını anlamak ve bu verileri işlemek için temel bir başlangıç noktası sağlar.

Ayrıca, veri kümesini yüklemek ve işlemek için Hugging Face kütüphanesini kullanabilirsiniz:

```python
from datasets import load_dataset

# Veri kümesini yükleme
dataset = load_dataset("mlabonne/orpo-dpo-mix-40k")

# Veri kümesinin ilk örneğini yazdırma
print(dataset["train"][0])
```

Bu kod, Hugging Face kütüphanesini kullanarak "mlabonne/orpo-dpo-mix-40k" veri kümesini yükler ve ilk örneği yazdırır.

1.  `load_dataset` fonksiyonu, belirtilen veri kümesini Hugging Face veri kümesi deposundan yükler.
2.  `dataset["train"][0]`, veri kümesinin eğitim bölümündeki ilk örneğe erişir.
3.  `print` ifadesi, ilk örneği yazdırır.

Bu kod, büyük veri kümelerini işlemek ve DPO algoritmasını uygulamak için yararlı olabilir.

---

