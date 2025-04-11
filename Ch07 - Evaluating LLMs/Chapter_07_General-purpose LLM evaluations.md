# Genel Amaçlı Değerlendirmeler
Genel amaçlı değerlendirmeler, temel ve genel amaçlı ince ayarlı modellere (fine-tuned models) yönelik metrikleri ifade eder. Bu değerlendirmeler, belirli görevlere veya alanlara odaklanmadan bilgi ve kullanışlılık ile ilişkili olan yeteneklerin geniş bir yelpazesini kapsar. Bu, geliştiricilerin bu yeteneklere genel bir bakış açısı kazanmalarını, kendilerini rakiplerle karşılaştırmalarını ve güçlü ve zayıf yönleri belirlemelerini sağlar. Bu sonuçlara dayanarak, veri kümesini ve hiperparametreleri (hyperparameters) ayarlamak veya mimariyi (architecture) değiştirmek mümkündür.

Genel amaçlı değerlendirmeleri üç aşamada geniş çapta kategorize edebiliriz: ön eğitim (pre-training) sırasında, ön eğitimden sonra ve ince ayardan (fine-tuning) sonra.

## Ön Eğitim Sırasında
Ön eğitim sırasında, modelin nasıl öğrendiğini yakından izleriz, bu Bölüm 5'in sonunda gösterildiği gibidir. En basit metrikler düşük seviyelidir ve modellerin nasıl eğitildiğine karşılık gelir:
- **Eğitim kaybı (Training loss)**: Çapraz entropi kaybına (cross-entropy loss) dayanarak, modelin öngörülen olasılık dağılımı ile bir sonraki tokenın gerçek dağılımı arasındaki farkı ölçer.
- **Doğrulama kaybı (Validation loss)**: Eğitim kaybı ile aynı kaybı hesaplar, ancak genelleme yeteneğini değerlendirmek için bir kenara ayrılmış doğrulama kümesinde (validation set) hesaplanır.
- **Karışıklık (Perplexity)**: Çapraz entropi kaybının üssü, modelin verilere ne kadar "şaşırdığını" temsil eder (düşük değer daha iyidir).
- **Gradyan normu (Gradient norm)**: Potansiyel dengesizlikleri veya kaybolan/patlayan gradyanları (vanishing/exploding gradients) tespit etmek için eğitim sırasında gradyanların büyüklüğünü izler.

Bu aşamada HellaSwag (ortak akıl yürütme - common sense reasoning) gibi kıyaslamaları dahil etmek de mümkündür, ancak bu değerlendirmelere aşırı uyum riski vardır.

## Ön Eğitimden Sonra
Ön eğitimden sonra, temel modeli değerlendirmek için bir dizi değerlendirme kullanmak yaygındır. Bu dizi, dahili ve halka açık kıyaslamaları içerebilir. İşte halka açık ön eğitim değerlendirmelerinin yaygın olanlarının eksik olmayan bir listesi:
- **MMLU (bilgi - knowledge)**: Modelleri, ilkokuldan profesyonel seviyelere kadar 57 konu üzerinden çoktan seçmeli sorularla test eder.
- **HellaSwag (akıl yürütme - reasoning)**: Modelleri, çoklu seçeneklerden en makul sonu seçerek belirli bir durumu tamamlamaya zorlar.
- **ARC-C (akıl yürütme - reasoning)**: Modelleri, nedensel akıl yürütme gerektiren ilkokul seviyesindeki çoktan seçmeli fen soruları üzerinde değerlendirir.
- **Winogrande (akıl yürütme - reasoning)**: Özenle hazırlanmış cümlelerde zamir çözünürlüğü yoluyla ortak akıl yürütmeyi değerlendirir.
- **PIQA (akıl yürütme - reasoning)**: Günlük fiziksel etkileşimler hakkında sorular sorarak fiziksel ortak akıl yürütme anlayışını ölçer.

Bu veri kümelerinin birçoğu, genel amaçlı ince ayarlı modelleri değerlendirmek için de kullanılır. Bu durumda, temel model ile ince ayarlı model arasındaki belirli bir skor farkına odaklanırız. Örneğin, kötü bir ince ayar, MMLU tarafından ölçülen modelin bilgisini düşürebilir. Aksine, iyi bir ince ayar daha fazla bilgi kazandırabilir ve MMLU skorunu artırabilir. Bu, modelin bir test kümesine çok yakın verilerle ince ayarlı olup olmadığını belirlemeye de yardımcı olabilir. Örneğin, ince ayar aşamasında bir temel modelin MMLU skorunu 10 puan artırmak olasılık dışıdır. Bu, talimat verilerinin (instruction data) kirlenmiş olabileceğinin bir işaretidir.

## İnce Ayarlı Modeller için Değerlendirmeler
İnce ayarlı modellere (supervised fine-tuning - SFT ve preference alignment ile eğitilmiş modeller) ek olarak, bu modellerin kendi kıyaslamaları da vardır. Bu kıyaslamalar, ince ayarlı modellerin soruları anlama ve yanıtlama yeteneklerine bağlıdır. Özellikle, talimat takibi (instruction-following), çoklu tur görüşmesi (multi-turn conversation) ve ajansal becerileri (agentic skills) test ederler:
- **IFEval (talimat takibi - instruction following)**: Bir modelin, cevabında virgül kullanmama gibi belirli kısıtlamalarla talimatları takip etme yeteneğini değerlendirir.
- **Chatbot Arena (görüşme - conversation)**: İnsanların iki modeli karşılıklı görüşmelerde karşılaştırarak en iyi cevabı oyladığı bir çerçeve sağlar.
- **AlpacaEval (talimat takibi - instruction following)**: Chatbot Arena ile yüksek oranda ilişkili olan otomatik değerlendirme.
- **MT-Bench (görüşme - conversation)**: Modelleri, çoklu tur görüşmelerde değerlendirir, bağlamı koruma ve tutarlı yanıtlar verme yeteneklerini test eder.
- **GAIA (ajansal - agentic)**: Araç kullanımı ve web taraması gibi geniş bir yetenek yelpazesini, çok adımlı bir şekilde test eder.

Bu değerlendirmelerin nasıl tasarlandığını ve kullanıldığını anlamak, uygulamanız için en iyi LLM'yi (Large Language Model) seçmek açısından önemlidir. Örneğin, bir modeli ince ayarlamak istiyorsanız, belirli bir boyut için bilgi ve akıl yürütme açısından en iyi temel modele sahip olmak istersiniz. Bu, farklı LLM'lerin yeteneklerini karşılaştırmanıza ve ince ayarınız için en güçlü temeli sunacak olanı seçmenize olanak tanır.

Bu kıyaslamalar popüler ve kullanışlı olsa da, doğası gereği bazı kusurları da vardır. Örneğin, halka açık kıyaslamalar, test verileri veya kıyaslama veri kümelerine çok benzeyen örnekler üzerinde modelleri eğiterek manipüle edilebilir. İnsan değerlendirmesi bile mükemmel değildir ve genellikle uzun ve güven dolu cevaplara, özellikle de düzgün biçimlendirildiklerinde (örneğin, Markdown kullanılarak), karşı önyargılıdır. Diğer taraftan, özel test kümeleri halka açık olanlar kadar çok incelenmemiştir ve kendi sorunları ve önyargıları olabilir. Bu, kıyaslamaların tek bir gerçeklik kaynağı olmadığını, ancak işaretler olarak kullanılması gerektiğini gösterir. Birden fazla değerlendirme benzer bir cevap sağladığında, bir modelin gerçek yetenekleri hakkında güven seviyenizi yükseltebilirsiniz.

### Kod Açıklaması
Yukarıdaki metinde doğrudan kod bulunmamaktadır. Ancak, metinde geçen bazı teknik terimlerin ve kavramların açıklanması ve örnek kod parçaları ile desteklenmesi mümkündür. Örneğin, çapraz entropi kaybı (cross-entropy loss), gradyan normu (gradient norm) gibi kavramlar, derin öğrenme kütüphaneleri (örneğin PyTorch veya TensorFlow) kullanılarak hesaplanabilir.

Örneğin, PyTorch kullanarak çapraz entropi kaybını hesaplamak için:
```python
import torch
import torch.nn as nn

# Örnek tensorler
outputs = torch.randn(10, 5)  # 10 örnek, 5 sınıf
labels = torch.randint(0, 5, (10,))  # 10 etiket

# Çapraz entropi kaybı hesaplama
loss_fn = nn.CrossEntropyLoss()
loss = loss_fn(outputs, labels)
print("Çapraz Entropi Kaybı:", loss.item())
```

Gradyan normunu izlemek için:
```python
# Model ve optimizer tanımlama
model = ...  # Tanımlanmış bir model
optimizer = torch.optim.SGD(model.parameters(), lr=0.01)

# Eğitim döngüsü içinde
for inputs, labels in training_data:
    optimizer.zero_grad()
    outputs = model(inputs)
    loss = loss_fn(outputs, labels)
    loss.backward()
    optimizer.step()
    
    # Gradyan normunu hesaplama
    gradient_norm = torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=float('inf'))
    print("Gradyan Normu:", gradient_norm.item())
```

Bu örnekler, metinde geçen bazı kavramların pratik uygulamalarını göstermektedir.

---

