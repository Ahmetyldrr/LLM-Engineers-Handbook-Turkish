#Bu Bölümün Çevirisi
Bu bölüm, Büyük Dil Modelleri'ni (LLMs) geliştirmek için tercih hizalama tekniklerini (preference alignment techniques) araştırdı. Tercih veri kümeleri (preference datasets) kavramını tanıtarak, onların yapısını ve insan tercihlerini ayrıntılı olarak yakalamadaki önemini açıkladı. Gerçek makalelerden alınan orijinal ve yapay zeka tarafından oluşturulan metinleri karşılaştırarak kendi özel tercih veri oluşturma işlem hattımızı (custom preference data generation pipeline) uyguladık. Bu işlem hattı, kullanım durumunuza göre yeniden kullanılabilir ve özelleştirilebilir. Ayrıca, RLHF'in (Reinforcement Learning from Human Feedback) evrimine genel bir bakış sunduk ve daha basit ve verimli bir alternatif olarak DPO'nun (Direct Preference Optimization) tanıtılmasına yol açtık. Son olarak, Unsloth kütüphanesini kullanarak DPO'yu, 5. Bölüm'den TwinLlama-3.1-8B modelimizi fine-tune etmek için uyguladık. Adım adım eğitimimiz, modeli eğitmek için pratik talimatlar verdi, ayrıca SFT'den (Supervised Fine-Tuning) önemli farklılıkları vurguladı. Nihai model, Hugging Face Hub'da mevcuttur. 
İngilizce hali:
This chapter explored preference alignment techniques for improving LLMs. It introduced the concept of preference datasets, explaining their structure and importance in capturing nuanced human preferences. We implemented our own custom preference data generation pipeline by comparing original and AI-generated text from real articles. This pipeline can be reused and customized based on your use case. We also provided an overview of the evolution of RLHF, leading to the introduction of DPO as a simpler and more efficient alternative. Finally, we implemented DPO using the Unsloth library to fine-tune our TwinLlama-3.1-8B model from Chapter 5 . Our step-by-step tutorial gave practical instructions for training the model, as well as highlighting key differences from SFT. The final model is available on the Hugging Face Hub.

#Sonraki Bölümün Çevirisi
Bir sonraki bölümde, Büyük Dil Modeli değerlendirmesinin (LLM evaluation) kritik konusunu ele alacağız, Büyük Dil Modeli performansını değerlendirmedeki zorlukları ve mevcut yaklaşımları ele alacağız. Alan-specifik değerlendirme setlerinin (domain-specific evaluation sets) oluşturulmasını ele alacağız, değerlendirme neden hala alanda kalıcı bir sorun olarak kaldığını inceleyeceğiz ve daha büyük modelleri daha küçük olanları değerlendirmek için kullanma (LLM-as-a-judge) kavramını tanıtacağız. Bölüm, kapsamlı bir değerlendirme işlem hattı (comprehensive evaluation pipeline) ile sona erecek ve tutarlı ve etkili Büyük Dil Modeli değerlendirmesi için yapılandırılmış bir çerçeve sunacaktır.
İngilizce hali:
In the next chapter, we will explore the crucial topic of LLM evaluation, addressing the challenges and current approaches in assessing LLM performance. We’ll cover the creation of domain-specific evaluation sets, examine why evaluation remains a persistent problem in the field, and introduce the concept of using larger models to evaluate smaller ones (LLM-as-a-judge). The chapter will conclude with a comprehensive evaluation pipeline, providing a structured framework for consistent and effective LLM evaluation.

Kod bulunmamaktadır, ancak metinde geçen teknik terimlerin açıklamaları ve İngilizce karşılıkları parantez içinde verilmiştir.

Eğer kod olsaydı, kodların açıklamaları şöyle olurdu:

*   Kodların her bir satırı ayrı ayrı açıklanırdı.
*   Örneğin bir python kodunu ele alalım: `model = TwinLlama-3.1-8B()`
    *   Bu satır, TwinLlama-3.1-8B adlı modeli `model` değişkenine atar.
*   Başka bir örnek: `model.train(data)`
    *   Bu satır, `model` adlı modeli `data` adlı veri kümesiyle eğitir.

Ancak bu metinde kod bulunmamaktadır.

---

