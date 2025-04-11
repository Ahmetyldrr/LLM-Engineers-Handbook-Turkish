#RAG Kullanmadan Chatbotların Güvenilirliği

Eğer RAG (Retrieval-Augmented Generation) olmayan bir chatbot'a eğitilmediği bir konuda soru sorulursa, doğru olmayan bir konuda size güvenilir bir cevap verme ihtimali yüksektir. 2024 Avrupa Futbol Şampiyonası'nı örnek olarak alalım. Eğer model Ekim 2023'e kadar eğitilmişse ve biz turnuva hakkında bir şeyler sorarsak, büyük olasılıkla gerçeklik ve doğruyu ayırt etmesi zor olan rastgele bir cevap üretecektir. LLM (Large Language Model) her zaman halüsinasyon görmese bile, cevaplarının güvenilirliği konusunda endişeler uyandırmaktadır. Bu nedenle, kendimize "LLM'nin cevaplarına ne zaman güvenebiliriz?" ve "Cevapların doğru olup olmadığını nasıl değerlendirebiliriz?" sorularını sormalıyız. RAG'ı tanıttığımızda, LLM'yi her zaman tanıttığımız bağlam (context) temelinde cevap vermeye zorlarız. LLM, akıl yürütme motoru (reasoning engine) olarak hareket ederken, RAG aracılığıyla eklenen ek bilgiler, oluşturulan cevap için tek gerçek kaynağı (single source of truth) olarak hareket edecektir. Bunu yaparak, LLM'nin cevabının harici verilere (external data) dayanıp dayanmadığını hızlı bir şekilde değerlendirebiliriz.

İngilizce teknik terimlerin parantez içinde Türkçe karşılığı ile birlikte yazılmış hali:

- RAG (Retrieval-Augmented Generation) 
- LLM (Large Language Model) 
- Context (Bağlam)

Kodların açıklaması kısmında herhangi bir kod bulunmamaktadır, çünkü verilen metin bir kod değil, bir İngilizce paragrafıdır. Ancak isterseniz, metni ayrıntılı olarak satır satır açıklayabilirim.

1. `If a chatbot without RAG is asked a question about something it wasn’t trained on, there is a high chance that it will give you a confident answer about something that isn’t true.`
   - RAG içermeyen bir chatbot'a eğitilmediği bir konu hakkında soru sorulduğunda, doğru olmayan bir konuda güvenilir bir cevap verme olasılığı yüksektir.

2. `Let’s take the 2024 European Football Championship as an example.`
   - 2024 Avrupa Futbol Şampiyonası'nı örnek olarak alalım.

3. `If the model is trained up to October 2023 and we ask it something about the tournament, it will most likely come up with a random answer that is hard to differentiate between reality and truth.`
   - Model Ekim 2023'e kadar eğitilmişse ve turnuva hakkında bir şeyler sorulursa, büyük olasılıkla gerçeklik ve doğruyu ayırt etmesi zor olan rastgele bir cevap üretecektir.

4. `Even if the LLM doesn’t hallucinate all the time, it raises concerns about the trustworthiness of its answers.`
   - LLM her zaman halüsinasyon görmese bile, cevaplarının güvenilirliği konusunda endişeler yaratmaktadır.

5. `Thus, we must ask ourselves: “When can we trust the LLM’s answers?” and “How can we evaluate if the answers are correct?”.`
   - Bu nedenle, kendimize "LLM'nin cevaplarına ne zaman güvenebiliriz?" ve "Cevapların doğru olup olmadığını nasıl değerlendirebiliriz?" sorularını sormalıyız.

6. `By introducing RAG, we enforce the LLM to always answer solely based on the introduced context.`
   - RAG'ı tanıttığımızda, LLM'yi her zaman tanıttığımız bağlam temelinde cevap vermeye zorlarız.

7. `The LLM will act as the reasoning engine, while the additional information added through RAG will act as the single source of truth for the generated answer.`
   - LLM, akıl yürütme motoru olarak hareket ederken, RAG aracılığıyla eklenen ek bilgiler, oluşturulan cevap için tek gerçek kaynağı olarak hareket edecektir.

8. `By doing so, we can quickly evaluate if the LLM’s answer is based on the external data or not.`
   - Bunu yaparak, LLM'nin cevabının harici verilere dayanıp dayanmadığını hızlı bir şekilde değerlendirebiliriz.

---

