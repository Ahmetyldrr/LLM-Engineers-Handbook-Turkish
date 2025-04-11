# Veri Kalitesi Değerlendirmesi (Data Quality Evaluation)

Veri kalitesi değerlendirmesi, makine öğreniminin (Machine Learning) kritik bir yönüdür, özellikle Büyük Dil Modelleri (LLMs - Large Language Models) için. Bu süreç, veri setlerinin doğruluk (accuracy), çeşitlilik (diversity) ve karmaşıklık (complexity) gibi çeşitli özelliklerini değerlendirmeyi içerir. Matematiksel doğruluk gibi bazı yönler Python yorumlayıcıları (Python interpreters) gibi araçlar kullanılarak kolayca doğrulanabilirken, öznel (subjective) veya açık uçlu (open-ended) içeriklerin değerlendirilmesi zorlu bir görevdir. Geleneksel veri kalitesi değerlendirme yöntemleri arasında yüksek doğruluk sağlayan ancak kaynak yoğun olan insan açıklaması (human annotation) bulunur. Ölçeklenebilirlik sorunlarını ele almak için, değerlendirme sürecini otomatize etmek amacıyla makine öğrenimi teknikleri geliştirilmiştir. Bunlar arasında hakem olarak LLMs (LLM-as-a-judge), ödül modelleri (reward models) ve kalite tahmini için eğitilmiş sınıflandırıcılar (classifiers) bulunur.

# Hakem Olarak LLMs (LLM-as-a-judge)

Hakem olarak LLMs stratejisi, her bir örneğin kalitesini değerlendirmek için LLMs'i yönlendirmeyi (prompting) içerir. Bu yaklaşım, esnekliği ve kullanım kolaylığı nedeniyle popüler hale gelmiştir, ancak bazı zorlukları da beraberinde getirir. Farklı LLMs, görevler arasında farklı performans seviyelerine sahiptir ve değerlendirmeleri genellikle uzman olmayanlarınkine daha yakın olur. Alan-specifik veri setleri için, genel amaçlı LLMs yerine alan-specifik modeller kullanmak isteyebilirsiniz. Karşılaştırmalı değerlendirme yöntemleri (örneğin, “A cevabı B'den daha mı iyi?”) genellikle mutlak puanlama yaklaşımlarından (örneğin, “A cevabını 1 ile 4 arasında derecelendirin”) daha iyi performans gösterir, ancak her ikisi de yeterli yönlendirme mühendisliği (prompt engineering) ile ölçekte kullanılabilir. Temsili bir alt küme üzerinde farklı yönlendirmeler üzerinde yineleme yaparak yanıtların kalitesini manuel olarak doğrulamayı öneririz. Tablo 5.2, bir hakem LLM için özel bir yönlendirmenin örneğini gösterir.

## Tablo 5.2 – Veri Kalitesi Değerlendirmesi için LLM-as-a-judge Yönlendirme Örneği
```
Talimat (Instruction): Bir veri kalitesi değerlendiricisisiniz. 
Göreviniz, bir talimatı ve buna karşılık gelen cevabı değerlendirmek ve cevabın verilen görevi ne kadar etkili bir şekilde ele aldığını belirlemektir. 
Değerlendirmenizde, cevabın güçlü ve zayıf yönlerini detaylandırarak 1 ila 4 arasında bir puan vereceksiniz. 
1 puan, cevabın berbat ve talimatla ilgisiz olduğu anlamına gelir. 
2 puan, cevabın yardımcı olmadığı ve talimatın önemli yönlerini kaçırdığı anlamına gelir. 
3 puan, cevabın yardımcı olduğu ancak uygunluk, doğruluk ve derinlik açısından geliştirilebileceği anlamına gelir. 
4 puan, cevabın mükemmel olduğu ve görevi tam olarak yerine getirdiği anlamına gelir. 
Değerlendirmenizi şu şekilde sağlayın: 
Geri Bildirim: (ilgili bulduğunuz güçlü ve zayıf yönler) 
Puan: (1 ile 4 arasında bir sayı)
```

### Kodun Açıklaması:
- `Talimat`: Değerlendiricinin rolünü tanımlar.
- `Göreviniz`: Değerlendiricinin yapması gereken görevi açıklar.
- `Değerlendirmenizde`: Değerlendirme kriterlerini ve puanlama sistemini açıklar.
- `Geri Bildirim` ve `Puan`: Değerlendirmenin nasıl formatlanacağını belirtir.

Hakem olarak LLMs'in çeşitli önyargıları (biases) olduğu bilinmektedir. İlk olarak, karşılaştırmalı puanlamada bir konum önyargısı (position bias) vardır; burada LLM hakemi ilk cevabı tercih eder. Bu, cevapların sırasını rastgeleleştirerek giderilebilir. Ayrıca, insanlar gibi, LLM hakemleri de uzun cevapları tercih eder. Uzunluk normalizasyonu teknikleri, mutlak puanlamada bu sorunu hafifletmek için uygulanabilir. Son olarak, LLM hakemlerinin intra-model tercihi (intra-model favoritism) olduğu bilinmektedir; yani aynı aileden modelleri tercih ederler (örneğin, GPT-4o ile GPT-4 ve GPT-4o mini). Bu, tek bir model yerine birkaç model kullanılarak giderilebilir. Genel olarak, değerlendirme güvenilirliğini artırmak için, bir jüri olarak birden fazla LLMs kullanmak gibi stratejiler önyargıyı azaltır ve tutarlılığı artırır. Daha küçük LLMs'lerden oluşan bir jüri kullanmak, maliyetleri düşürürken doğruluğu artırabilir ve intra-model tercihini azaltabilir. Sohbet robotları gibi belirli uygulamalar için, LLM hakemleri ile insan değerlendiriciler arasında yüksek uyum (~%80) hedeflenmesi tavsiye edilir. Basit derecelendirme ölçekleri (few-shot prompting ile) ve göreve özgü ölçütler de ilgili ve yorumlanabilir değerlendirmeler sağlamak için önerilir.

# Ödül Modelleri (Reward Models)

Ödül modelleri, LLMs'i veri kalitesi değerlendirmesi için yeniden kullanmanın başka bir yoludur. "Ödül modeli" terimi, İnsan Geri Bildiriminden Güçlendirmeli Öğrenme (RLHF - Reinforcement Learning from Human Feedback, Bölüm 6'ya bakınız)'den gelir. Geniş anlamda, bir talimat ve cevap çiftini alır ve çıktı olarak bir puan döndüren modeller olarak tanımlanabilirler. Genellikle, ödül modelleri, Gemma veya Llama gibi bir decoder-only mimarisinin üzerine doğrusal bir başlık (linear head) eklenerek oluşturulur. Daha sonra bu özel amaç için, ya güçlendirmeli öğrenme ya da geleneksel fine-tuning kullanılarak eğitilirler. Şekil 5.3, bir Llama 3 8B modelinin üzerine regresyon ve kapılama katmanları (regression and gating layers) ekleyen ArmoRM-Llama3-8B-v0.1'in mimarisini gösterir. Bu model, yardımseverlik (helpfulness), doğruluk (correctness), tutarlılık (coherence), karmaşıklık (complexity) ve ayrıntı (verbosity) gibi belirli boyutları hedefleyen birden fazla puan çıktılar. Bu, veri kalitesi değerlendirmesi için daha ayrıntılı bir yaklaşım sağlar.

## Şekil 5.3 – RLHFlow/ArmoRM-Llama3-8B-v0.1'in Mimarisi (Kaynak: https://doi.org/10.48550/arXiv.2406.12845)

Allen Institute for AI'ın Hugging Face üzerinde barındırdığı RewardBench lider tablosu (allenai/reward-bench), farklı ödül modellerini karşılaştırmak için iyi bir kaynaktır. Çeşitli ödül modeli türlerini (üretken, sınıflandırıcılar, DPO, vb.) birleştirir ve her talimat için seçilen ve reddedilen cevaplar üzerinde değerlendirir. Bu görev, talimat veri kalitesi ile doğrudan ilgili olmamakla birlikte, iyi ve kötü cevapları ayırt edebilen modelleri bulmak için iyi bir kaynaktır.

# Sınıflandırıcılar (Classifiers)

Sınıflandırıcılar veya encoder-only modeller, veri kalitesi değerlendirmesi yapmak üzere eğitilebilir. İyi bir örnek, web sayfalarının eğitimsel değerini değerlendirmek üzere tasarlanmış HuggingFaceFW/fineweb-edu-classifier'dir. Bu model, ön eğitim verileri için bir kalite filtresi olarak tasarlanmıştır, ancak benzer bir yaklaşım, talimat örneklerini ölçekte değerlendirmek için de uygulanabilir. Uygulamada, fineweb-edu-classifier, bir embedding modeline (Snowflake/snowflake-arctic-embed-m) bir sınıflandırma başlığı ekler ve Llama 3 70B Instruct tarafından açıklanan 450.000 örnek üzerinde 20 epoch boyunca eğitilir. Bu yaklaşım, hem daha küçük hem de sınıflandırma görevlerine daha uygun olan encoder-only modellere dayanır. Düşük parametre sayıları sayesinde, bu modeller daha hızlı çalışır ve milyonlarca örneğe ölçeklenebilir. Ancak, özellikle nüansları yakalama yetenekleri eksik olduğu karmaşık muhakeme görevlerinde, daha büyük modeller kadar doğru değildirler. Daha küçük ölçekte, encoder-only modeller, aykırı değerleri filtrelemek veya daha hızlı işlem gerektiren otomatik bir veri hattının parçası olarak hala değerlidir.

---

