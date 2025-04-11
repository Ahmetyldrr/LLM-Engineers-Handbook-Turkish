# Veri Değerlendirme

Veri değerlendirme, insan değerlendiriciler tarafından veya LLM'ler (Large Language Models - Büyük Dil Modelleri) ile otomatik olarak gerçekleştirilebilir. LLM değerlendirmesi, ayrıntılı kriterler geliştirmeyi, bu yönergeleri LLM'ye açıkça ileten bir istem (prompt) oluşturmayı ve modeli tercih edilen ve reddedilen yanıtları seçmek için kullanmayı içerir. İnsan değerlendirmesinden daha ölçeklenebilir olmasına ve kriterlerin tutarlı bir şekilde uygulanmasına izin vermesine rağmen, bu LLM değerlendirme kalitesi doğrudan modelin performansı ve sağlanan yönergelerle ilgilidir. İnce insan tercihlerini veya kültürel nüansları kaçırabilir. Ancak, LLM'ler gelişmeye devam ettikçe, nüanslı yargılarda bulunma yetenekleri de gelişir ve potansiyel olarak zamanla daha yüksek kaliteli veri kümelerine yol açar.

# Değerlendirme Yöntemleri

LLM değerlendirmesini tercih veri kümeleri için uygulamak, mutlak puanlama (absolute scoring) veya ikili sıralama (pairwise ranking) yoluyla yapılabilir. Mutlak puanlamada, LLM, önceden tanımlanmış kriterlere göre her yanıta sayısal bir puan veya kategorik bir derecelendirme atar. Bu yöntem basit ama farklı istemler veya değerlendirme oturumları arasında tutarsızlıktan zarar görebilir. İkili sıralama ise LLM'ye iki yanıt sunmayı ve hangisinin daha iyi olduğunu seçmesini veya sıralamasını istemeyi içerir. Bu yaklaşım, insan değerlendirmesinin biçimini daha yakından taklit eder ve daha tutarlı sonuçlara yol açabilir.

# İstem Örnekleri

Mutlak puanlama için, değerlendirme kriterlerini özetleyen ve LLM'den yanıtı belirli bir ölçekte derecelendirmesini isteyen bir istem oluşturursunuz (örneğin, 1-5 veya kötü/orta/iyi/mükemmel). İstem şöyle görünebilir: 
“Alakalı olma, tutarlılık ve yararlılık temelinde aşağıdaki yanıtı 1-5 arasında bir ölçekte derecelendir: [YANITI EKLEYİN].” 
İkili sıralama için istem şöyle olabilir: 
“Aşağıdaki iki yanıtı karşılaştırın. Alakalı olma, tutarlılık ve yararlılık açısından hangisi daha iyidir? Yanıt A: [YANIT A] Yanıt B: [YANIT B].”

# Tercih Veri Kümeleri için İkili Sıralama

Tercih veri kümelerinin karşılaştırmalı doğası, ikili sıralamayı değerlendirme için ideal bir yaklaşım haline getirir. Bu yöntem genellikle mutlak puanlamadan daha doğru ve insan yargısıyla daha yakından ilişkilidir. İkili sıralama, insanların seçenekleri karşılaştırma şeklini doğal olarak taklit eder ve hem insan değerlendiricilerin hem de LLM'lerin tutarlı ve anlamlı değerlendirmeler sağlamasını kolaylaştırır.

# Doğruluk İyileştirmesi

İkili sıralamanın doğruluğunu, bir referans yanıt (ground-truth answer) sağlayarak ve düşünce zinciri akıl yürütmeyi (chain-of-thought reasoning) kullanarak daha da geliştirebiliriz. Bu yaklaşım, değerlendirme yapan LLM'yi yanıtların birden fazla yönünü dikkate almaya ve karar verme sürecini açıklığa kavuşturmaya teşvik eder, böylece daha kapsamlı ve gerekçeli değerlendirmelere yol açar. Referans yanıt mevcut olmadığında, LLM'den bir not (grading note) oluşturmasını istemek mümkündür; bu, beklenen yanıtın bir tanımıdır. Bu teknik, LLM'nin belirli bir konu hakkında kapsamlı bilgiye sahip olmadığı senaryolarda özellikle iyi çalışır, çünkü modelin yanıtları değerlendirmesinden önce değerlendirme için açık kriterler belirlemesini zorlar.

# LLM-Hakim Prompt Örneği

İkili sıralama gerçekleştirmek için bir LLM-hakim (LLM-as-a-judge) prompt'unun somut bir uygulaması:
```markdown
# Görev
Bir cevap hakemisin. Amacın, A ve B cevaplarını karşılaştırmak. Talimatı cevap verme açısından hangisinin daha iyi bir iş çıkardığını, alakalı olma, doğruluk, bütünlük, açıklık, yapı ve özlük açısından bilmek istiyorum.

Talimat: {instruction}
Cevap A: {answer_a}
Cevap B: {answer_b}

Karar verme sürecini adım adım açıkla ve en iyi cevabın harfini aşağıdaki yapıyı kullanarak çıktılayınız:
Akıl Yürütme: (iki cevabı karşılaştır)
En İyi Cevap: (A veya B)
```

# Tablo 6.2 – İkili Sıralama için LLM-Hakim Prompt Örneği

| Görev | Talimat | Cevap A | Cevap B | Akıl Yürütme | En İyi Cevap |
| --- | --- | --- | --- | --- | --- |

# LLM Tabanlı Değerlendirmenin Sınırlamaları

LLM tabanlı değerlendirmenin çeşitli önyargı türlerine tabi olabileceğini belirtmek önemlidir:
- **Konum Önyargısı (Position Bias)**: Göreceli puanlamada, LLM hakimler ilk sunulan cevabı tercih etme eğilimindedir.
- **Uzunluk Önyargısı (Length Bias)**: İnsanlar gibi, LLM hakimler de genellikle daha uzun cevapları tercih eder, potansiyel olarak daha kısa, daha özlü yanıtların kalitesini göz ardı eder.
- **Aile Önyargısı (Family Bias)**: LLM hakimler, dil kalıpları veya bilgi tabanlarındaki benzerlikler nedeniyle kendileri veya aynı aileden modeller tarafından üretilen yanıtları tercih edebilir.

# Önyargıları Azaltma

Bu önyargıları azaltmak ve tercih veri kümelerinin kalitesini artırmak için çeşitli çözümler uygulanabilir. 
1. **Karşılaştırma Sırasını Rastgele Hale Getirme**: Her karşılaştırmada A ve B cevaplarının sırasını rastgele hale getirmek, konum önyargısını dengeleyebilir.
2. **Dengeli Puan Dağılımı Gösteren Az Şekilli Örnekler Sağlama**: Bu örnekler, hakim LLM'nin iç puanlama mekanizmasını kalibre etmeye hizmet eder ve hem uzunluk hem de aile önyargısını etkili bir şekilde ele alabilir.
3. **Çoklu Model Kullanma**: Tek bir LLM hakime güvenmek yerine, bir jüri olarak birden fazla model kullanmak, değerlendirme sürecinin sağlamlığını önemli ölçüde artırabilir.

# Kod Açıklaması

Yukarıdaki metinde verilen kod örneği bir LLM-hakim prompt'unun nasıl yapılandırılacağını gösterir. 
- `{instruction}`: Değerlendirilecek talimatı temsil eder.
- `{answer_a}` ve `{answer_b}`: Karşılaştırılacak iki cevabı temsil eder.
- `Akıl Yürütme`: LLM'den iki cevabı adım adım karşılaştırmasını ve karar verme sürecini açıklaması istenir.
- `En İyi Cevap`: LLM, daha iyi olan cevabın harfini (A veya B) çıktılar.

Bu prompt yapısı, LLM'nin cevapları sistematik bir şekilde değerlendirmesini ve karşılaştırmasını sağlar, böylece daha tutarlı ve gerekçeli değerlendirmelere olanak tanır.

İngilizce teknik terimler parantez içinde eklenmiştir:
- Large Language Models (LLM - Büyük Dil Modelleri)
- Prompt (İstem)
- Absolute Scoring (Mutlak Puanlama)
- Pairwise Ranking (İkili Sıralama)
- Ground-truth Answer (Referans Yanıt)
- Chain-of-Thought Reasoning (Düşünce Zinciri Akıl Yürütme)
- LLM-as-a-Judge (LLM-Hakim)

---

