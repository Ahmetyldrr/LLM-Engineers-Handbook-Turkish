# Sentetik Veri Oluşturma (Synthetic Data Generation)

Mevcut talimat veri kümeleri (instruction datasets) yeterli olmadığında, özel veri oluşturmak gerekli hale gelir. Bu, özellikle halka açık veri kaynaklarının kıt olduğu özel uygulamalar için geçerlidir. Ayrıca, bir veri kümesindeki eksik temsil edilen alanları, örneğin önceki örneğimizde JavaScript hata işleme (error-handling) tekniklerine ilişkin yetersiz örnekleri, artırmak için bir yöntem olarak hizmet eder. Veriler, bireyler tarafından manuel olarak veya crowdsourcing yoluyla oluşturulabilirken, bu yaklaşımlar genellikle önemli maliyetler ve zaman yatırımları gerektirir. Büyük Dil Modelleri (LLMs) kullanarak sentetik veri oluşturma, daha verimli ve ölçeklenebilir bir alternatif sunar. Bu yöntem, iyi tasarlanmış istem mühendisliği (prompt engineering) ile birleştirildiğinde, çok daha büyük ölçekte yüksek kaliteli veri üretebilir ve manuel veri oluşturma işlemlerinin sınırlamalarını etkili bir şekilde ele alır.

Sentetik veri oluşturma süreci genellikle bir dizi özenle tasarlanmış istemlerin (bazen taksonomi olarak adlandırılır) hazırlanmasıyla başlar. Bunlar, yeni, çeşitli örnekler oluşturmak için temel oluşturur. Orijinal Alpaca veri kümesinde kullanılan beş tohum istemi (seed prompts) Tablo 5.3'te görülebilir.

```
Tablo 5.3 – Orijinal Alpaca veri kümesinde kullanılan tohum istem örnekleri
Tohum Talimatları (Seed Instructions) 
- Yumurta içermeyen, protein içeren ve yaklaşık 700-1000 kalori olan breakfast için ne yiyebilirim? 
- Verilen çiftler arasındaki ilişki nedir? Girdi (Input): Gece : Gündüz :: Sağ : Sol 
- Aşağıdaki kişiler için birer cümlelik açıklama yapın. Girdi: -Barack Obama\n- Elon Musk\n- Taylor Swift 
- Verilen stereotip size nasıl zarar verebilir? Girdi: Tüm Asialılar zeki! 
- Aşağıdaki e-posta için uygun öznel bir başlık oluşturun. Girdi: “Merhaba [kişi adı],\n\nCVPR'deki multimodality çalıştayında panelist olmaktan memnun olup olmadığınıza dair size yazıyorum. Çalıştay 20 Haziran 2023'te yapılacak. \n\nSaygılarımla,\n[isim]”
```

Sentetik olarak oluşturulan verilerin kalitesi, büyük ölçüde oluşturma sürecinde kullanılan istemlere ve tekniklere bağlıdır. İyi hazırlanmış istemler, dil modelinin çeşitli, ilgili ve yüksek kaliteli talimat-cevap çiftleri üretmesini sağlayabilir. Bu istemler genellikle belirli talimatları, örnekleri ve kısıtlamaları içerir ve oluşturulan verilerin istenen biçim ve içerikle uyumlu olmasını sağlar.

Birçok sentetik veri oluşturma ardışık düzeni (pipelines), veri kalitesini sağlamak için birden fazla adım içerir. Bu, ilk olarak bir dizi soru veya talimat oluşturmayı, ardından karşılık gelen cevapları veya yanıtları oluşturmayı içerebilir. Bazı sistemler ayrıca, oluşturulan çiftlerin doğruluğunu, uygunluğunu ve belirtilen ölçütlere uyumluluğunu kontrol eden doğrulama adımları uygular.

Sentetik veri oluşturmanın önemli bir yönü, oluşturulan verilerin çeşitli özelliklerini kontrol etme yeteneğidir. Bu, talimatların karmaşıklığı, yanıtların uzunluğu, kullanılan dilin tonu veya stili ve kapsanan belirli konular veya alanlar gibi faktörleri içerir. Bu parametreleri ince ayar yaparak, belirli eğitim amaçlarına yönelik veya mevcut veri kümelerini hedeflenen şekillerde tamamlayan veri kümeleri oluşturmak mümkündür.

Outlines gibi kütüphaneleri kullanarak yapılandırılmış oluşturma, belirli biçimlere uymak için de yararlı olabilir. Ayrıca, sentetik veri oluşturma, mevcut veri kümelerindeki yanlılıkları ve boşlukları ele almak için özellikle yararlı olabilir. Oluşturma sürecini dikkatlice tasarlayarak, daha dengeli ve kapsayıcı veri kümeleri oluşturmak mümkündür; bu veri kümeleri daha geniş bir perspektif, konu ve dil stillerini temsil eder. Bu, daha adil ve çeşitli kullanıcı tabanlarına hizmet edebilen Büyük Dil Modelleri (LLMs) eğitiminde yardımcı olabilir.

Ancak, sentetik veri oluşturma da zorluklarla birlikte gelir. Temel bir endişe, oluşturulan verilerin, oluşturma için kullanılan temel dil modelinden yanlılıkları veya hataları devralma potansiyelidir. Bunu azaltmak için, birçok yaklaşım insan gözetimini, çeşitli istemleri ve oluşturulan verilerin kalitesini ve uygunluğunu sağlamak için ek filtreleme mekanizmalarını içerir.

Diğer bir husus, oluşturulan verilerin yeterince çeşitli ve zorlayıcı olması gerektiğidir. Sentetik veri çok basit veya tekrarlı ise, sağlam bir Büyük Dil Modeli (LLM) eğitimi için gerekli karmaşıklık düzeyini sağlamayabilir. Sentetik veri oluşturmadaki gelişmiş teknikler genellikle, modelin öğrenebileceği sınırları zorlayabilen çeşitli ve nüanslı talimat-cevap çiftleri oluşturmaya odaklanır.

### Kodların Ayrıntılı Açıklaması

Yukarıdaki metinde doğrudan bir kod pasajı bulunmamaktadır. Ancak, sentetik veri oluşturma sürecinde kullanılan bazı teknikler ve kütüphanelerden bahsedilmektedir. Örneğin, "Outlines" kütüphanesinin yapılandırılmış oluşturma için kullanılabileceği belirtilmektedir.

Eğer bir örnek kod pasajı verilseydi, bu kodun altında satır satır açıklamalar yapılabilirdi. Örneğin, eğer bir Python kodu verilseydi:

```python
# Örnek kod
from outlines import generate

# Tohum istemi tanımlama
seed_prompt = "Yumurta içermeyen, protein içeren ve yaklaşık 700-1000 kalori olan breakfast için ne yiyebilirim?"

# Sentetik veri oluşturma
generated_data = generate(seed_prompt)

# Oluşturulan veriyi yazdırma
print(generated_data)
```

Bu kodun açıklaması şöyle olabilir:

1. `from outlines import generate`: Bu satır, "outlines" kütüphanesinden `generate` fonksiyonunu içe aktarır. Bu fonksiyon, sentetik veri oluşturmak için kullanılır.
2. `seed_prompt = "Yumurta içermeyen, protein içeren ve yaklaşık 700-1000 kalori olan breakfast için ne yiyebilirim?"`: Bu satır, bir tohum istemi tanımlar. Bu istem, sentetik veri oluşturma sürecini başlatmak için kullanılır.
3. `generated_data = generate(seed_prompt)`: Bu satır, `generate` fonksiyonunu çağırarak tanımlanan tohum istemi temelinde sentetik veri oluşturur.
4. `print(generated_data)`: Bu satır, oluşturulan sentetik veriyi yazdırır.

---

