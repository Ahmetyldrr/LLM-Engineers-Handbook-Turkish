#Talimat Veri Kümeleri (Instruction Datasets)

Talimat veri kümeleri (Instruction datasets), talimatlar ve cevaplar çiftleri olarak tanımlanır. Talimatlar, modelin girdileri (inputs) olup, ince ayar (fine-tuning) sırasında bağlam (context) olarak kullanılır. Cevaplar, modelin beklenen çıktılarıdır (expected outputs). İnce ayar sırasında, modeli talimatlar ve cevaplar üzerinde veya sadece cevaplar üzerinde eğitmek üzere seçebilirsiniz. Talimatlar ve cevaplar çiftleri belirli bir şablonu (template) takip eder. Alpaca gibi bazı talimat şablonları, girdiler (inputs) ve sistem (system) gibi ek alanlar (fields) içerir. Her ikisi de talimat alanının (instruction field) alt alanları olarak kabul edilebilir. Bu durumda, “girdiler” (inputs), modelin talimatı tamamlamak için ihtiyaç duyduğu verileri içerir ve “sistem”, modelin genel davranışını yönlendirmek için bir meta-istemdir (meta-prompt). İşte SlimOrca veri kümesinden (dataset) bir örnek, “sistem” ve “talimat” ile:

Sistem (System): Yardımsever bir asistsiniz, her zaman açıklama yapın. Beş yaşında bir çocuğa cevap veriyormuş gibi düşünün.
Talimat (Instruction): Kavramlar: bina, mağaza, kasaba (Concepts: building, shop, town)
Tüm bu kelimeleri içeren bir cümle yazın.
Çıktı (Output): Küçük kasabamızda, insanların en sevdiği oyuncakları ve şekerlemeleri satın almak için gittikleri büyük bir bina içinde bir mağaza var.

Tablo 5.1 – Open-Orca/SlimOrca veri kümesinden örnek

Bu örnek, “sistem” alanının model için belirli davranışları tanımlamak için nasıl kullanıldığını gösterir, örneğin yardımsever olmak, her zaman açıklamalar yapmak ve beş yaşında bir çocuğa hitap eder gibi cevaplar vermek. “Talimat” alanı, gerekli verileri (kavramları) ve görevi (bir cümle oluşturmak) sağlar. Çıktı alanı, beklenen cevabı gösterir, ki bu tek mümkün cevap olmasa da, yüksek kaliteli bir cevabı temsil eder.

Bir talimat veri kümesi oluşturmak için, modelin nasıl kullanılacağını temsil eden verileri seçmek isteriz. Yeterli örnek topladığımızda, amacımız bunları filtrelemek ve sadece yüksek kaliteli verileri tutmaktır. Bu bağlamda, yüksek kaliteli veriler üç ana boyut aracılığıyla tanımlanabilir:

- **Doğruluk (Accuracy)**: Örneklerin gerçek doğruluğunu ve alaka düzeyini ifade eder. Talimat veri kümeleri bağlamında, bu, cevapların sadece gerçekte doğru olmakla kalmayıp aynı zamanda ilgili talimatlara uygun olmasını sağlamak anlamına gelir. Yüksek doğruluk, güvenilir ve güvenilir bilgi sağlayabilen modellerin eğitimi için esastır.
- **Çeşitlilik (Diversity)**: Yüksek kaliteli bir veri kümesi, geniş bir kullanım senaryoları yelpazesini kapsamalıdır ve dağıtılan LLM'nin karşılaşabileceği potansiyel sorguları ve görevleri içermelidir. Bu çeşitlilik, konuları, bağlamları, metin uzunluklarını ve yazı stillerini kapsamalıdır. Verileri temsilci bir şekilde örnekleyerek, modellerin sağlam talimat takip etme yetenekleri geliştirmelerine olanak tanırız.
- **Karmaşıklık (Complexity)**: Önemsiz veya aşırı basit örnekler, bir LLM'nin yeteneklerini geliştirmek için çok az şey yapar. Bunun yerine, veri kümeleri, modelin ele alması beklenen sınırları zorlayan karmaşık, çok adımlı muhakeme problemleri ve zorlu görevler içermelidir. Bu karmaşıklık, gerçek dünya problemlerini ele alabilen modellerin geliştirilmesine yardımcı olur.

Aşağıdaki bölümlerde, bu boyutlara göre talimat örneklerini filtrelemek ve değerlendirmek için teknikleri göreceğiz.

**Kod Açıklaması:**

Yukarıdaki metinde kod bulunmamaktadır. Ancak, örnek olarak verilen tabloyu Python kullanarak oluşturmak istersek, aşağıdaki gibi bir kod yazabiliriz:

```python
# Örnek tablo verilerini tanımla
data = {
    "Sistem": ["Yardımsever bir asistansınız, her zaman açıklama yapın. Beş yaşında bir çocuğa cevap veriyormuş gibi düşünün."],
    "Talimat": ["Kavramlar: bina, mağaza, kasaba\nTüm bu kelimeleri içeren bir cümle yazın."],
    "Çıktı": ["Küçük kasabamızda, insanların en sevdiği oyuncakları ve şekerlemeleri satın almak için gittikleri büyük bir bina içinde bir mağaza var."]
}

# Pandas kütüphanesini kullanarak tabloyu oluştur
import pandas as pd

df = pd.DataFrame(data)

# Tabloyu yazdır
print(df)
```

Bu kod, örnek tabloyu bir pandas DataFrame'i olarak tanımlar ve yazdırır.

1. `data` değişkeni, tablo verilerini bir sözlük olarak tanımlar.
2. `pd.DataFrame(data)`, bu verileri bir DataFrame'e dönüştürür.
3. `print(df)`, DataFrame'i yazdırır.

Bu kod, örnek tabloyu oluşturmak için basit bir Python komut dosyasını gösterir.

---

