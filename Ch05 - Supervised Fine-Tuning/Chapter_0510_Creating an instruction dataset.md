#Ince Ayar (Fine-Tuning) Sürecinde Talimat Veri Kümesi Oluşturma
Çoğu kullanım durumunda, bir talimat veri kümesi (instruction dataset) oluşturmak ince ayar (fine-tuning) sürecinin en zor kısmıdır. Bunun nedeni birden fazla faktörden kaynaklanmaktadır. Çoğu kullanım durumu ham metin (raw text) ile bağlantılı olabilir, ancak doğal talimat ve cevap çiftleri bulmak nadirdir. Bu ham metnin (raw text) hem talimatları hem de cevapları içeren bir formata dönüştürülmesi gerekir. Ayrıca, verilerin kalitesi de çok önemlidir. Bu nedenle, bireysel örnekleri manuel olarak kontrol etmek ve doğrulamak için çok zaman harcanır. Bu dikkatli inceleme, veri kümesinin (dataset) doğru ve modelin eğitimi için yararlı olmasını sağlamaya yardımcı olur.

#Şekil 5.1 – Bu bölümde ele alınan eğitim sonrası veri ardışık düzeni (post-training data pipeline)
Bu bölümde, nihai kullanım durumundan bağımsız olarak kendi talimat veri kümelerinizi (instruction datasets) oluşturmak için genel bir çerçeve (general framework) tanıtacağız. Daha sonra, 3. Bölüm'den toplanan verileri (scraped data) kullanarak bir talimat veri kümesi (instruction dataset) oluşturacağız. Veri oluşturma ardışık düzenimizdeki (data generation pipeline) farklı aşamalar Şekil 5.1'de özetlenmiştir.

İngilizce Teknik Terimler Parantez İçinde:
#Ince Ayar (Fine-Tuning) Sürecinde Talimat Veri Kümesi (Instruction Dataset) Oluşturma
Çoğu kullanım durumunda, bir talimat veri kümesi (instruction dataset) oluşturmak ince ayar (fine-tuning) sürecinin en zor kısmıdır. Bunun nedeni birden fazla faktörden kaynaklanmaktadır. Çoğu kullanım durumu ham metin (raw text) ile bağlantılı olabilir, ancak doğal talimat (instruction) ve cevap (answer) çiftleri bulmak nadirdir. Bu ham metnin (raw text) hem talimatları (instructions) hem de cevapları (answers) içeren bir formata dönüştürülmesi gerekir. Ayrıca, verilerin kalitesi (data quality) de çok önemlidir. Bu nedenle, bireysel örnekleri (individual samples) manuel olarak kontrol etmek ve doğrulamak için çok zaman harcanır. Bu dikkatli inceleme (careful review), veri kümesinin (dataset) doğru (accurate) ve modelin eğitimi (model training) için yararlı olmasını sağlamaya yardımcı olur.

#Şekil 5.1 – Bu bölümde ele alınan eğitim sonrası veri ardışık düzeni (post-training data pipeline)
Bu bölümde, nihai kullanım durumundan (final use case) bağımsız olarak kendi talimat veri kümelerinizi (instruction datasets) oluşturmak için genel bir çerçeve (general framework) tanıtacağız. Daha sonra, 3. Bölüm'den toplanan verileri (scraped data) kullanarak bir talimat veri kümesi (instruction dataset) oluşturacağız. Veri oluşturma ardışık düzenimizdeki (data generation pipeline) farklı aşamalar Şekil 5.1'de özetlenmiştir (summarized).

Kodların Ayrıntılı Açıklaması:
Yukarıdaki metinde kod bulunmamaktadır. Ancak, başlıkları `#baslik` şeklinde yazmak istersek:
```markdown
#Ince Ayar (Fine-Tuning) Sürecinde Talimat Veri Kümesi Oluşturma
...
#Şekil 5.1 – Bu bölümde ele alınan eğitim sonrası veri ardışık düzeni (post-training data pipeline)
```
Bu şekilde yazılmış metinlerde, başlıklar `#` ile gösterilir ve okunurluğu artırır.

---

