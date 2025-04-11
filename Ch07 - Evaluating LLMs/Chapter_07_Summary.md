#Bu Bölümde Neler Öğrendik
Bu bölümde, LLM değerlendirmesini (LLM evaluation) modeller ve RAG sistemleri ile inceledik. MMLU gibi klasik ölçütleri (benchmarks) nasıl yorumlayacağımızı ve kullanacağımız veya ince ayar yapacağımız (fine-tune) güçlü adayları nasıl seçeceğimizi gördük. Ayrıca, alan-specifik (domain-specific) ve görev-specifik (task-specific) değerlendirmelerin nasıl çalıştığını ve halka açık örnekler temelinde kendi değerlendirmelerimizi nasıl oluşturacağımızı ayrıntılı olarak anlattık. Bu özel değerlendirme çerçevelerinin (custom evaluation frameworks) omurgası olarak iki teknikten (multiple-choice question answering ve LLM-as-a-judge) yararlandık. Ancak, modeller genellikle ek bağlam (additional context) sağlayan daha geniş sistemlere entegre edilir. RAG sistemleri için iki değerlendirme çerçevesi tanıttık: Ragas ve ARES. Hem benzerlikleri (örneğin, sentetik veri üretimi (synthetic data generation)) hem de RAG sistemlerini nasıl değerlendirdiklerindeki farklılıkları (bağlam-tabanlı ölçümler (context-based metrics) versus eğitilmiş sınıflandırıcılar (trained classifiers)) gördük. Son olarak, TwinLlama-3.1-8B'yi bir yargıç LLM ile üç kritere göre değerlendirdik: alaka (relevance), tutarlılık (coherence) ve özülük (conciseness). Bu, nasıl geliştirebileceğimize dair içgörüler sağladı. Bir sonraki bölümde, model performansından önemli ölçüde ödün vermeden hızı artırmak ve bellek kullanımını azaltmak için çıkarım optimizasyonu (inference optimization) tekniklerini inceleyeceğiz. Ayrıca, optimizasyon yöntemlerini, model paralelliği (model parallelism) tekniklerini ve farklı niceleme (quantization) yaklaşımlarını inceleyeceğiz.

İngilizce paragraf:
```
In this chapter, we explored LLM evaluation with models and RAG systems. We saw how to interpret classic benchmarks like MMLU to select strong candidates to use or fine-tune. We also detailed how domain-specific and task-specific evaluations work, and how to create our own based on publicly available examples. We focused on two techniques (multiple-choice question answering and LLM-as-a-judge) as the backbone of these custom evaluation frameworks. However, models are commonly integrated into broader systems that provide additional context. We introduced two evaluation frameworks for RAG systems, Ragas and ARES. We saw both similarities (for example, synthetic data generation) and differences in how they evaluate RAG systems (context-based metrics versus trained classifiers). Finally, we evaluated TwinLlama-3.1-8B with a judge LLM according to three criteria: relevance, coherence, and conciseness. This provided insights into how we can improve it. In the next chapter, we will explore inference optimization techniques to improve speed and reduce memory usage, without significantly compromising model performance. We will also delve into optimization methods, model parallelism techniques and examine different quantization approaches.
```

Kodların ayrıntılı açıklaması:
- `In this chapter`: Bu bölümde
- `we explored LLM evaluation with models and RAG systems`: LLM değerlendirmesini modeller ve RAG sistemleri ile inceledik.
  - `LLM evaluation`: Büyük Dil Modeli değerlendirme
  - `RAG systems`: Retrieval-Augmented Generation sistemleri
- `We saw how to interpret classic benchmarks like MMLU`: MMLU gibi klasik ölçütleri nasıl yorumlayacağımızı gördük.
  - `classic benchmarks`: klasik ölçütler/benchmarklar
  - `MMLU`: Massive Multitask Language Understanding, bir ölçüt
- `to select strong candidates to use or fine-tune`: kullanacağımız veya ince ayar yapacağımız güçlü adayları seçmek.
  - `fine-tune`: ince ayar yapmak
- `We also detailed how domain-specific and task-specific evaluations work`: Ayrıca, alan-specifik ve görev-specifik değerlendirmelerin nasıl çalıştığını ayrıntılı olarak anlattık.
  - `domain-specific`: alana özgü
  - `task-specific`: göreve özgü
- `and how to create our own based on publicly available examples`: ve halka açık örnekler temelinde kendi değerlendirmelerimizi nasıl oluşturacağımızı.
  - `publicly available examples`: halka açık örnekler
- `We focused on two techniques`: Bu özel değerlendirme çerçevelerinin omurgası olarak iki teknikten yararlandık.
  - `multiple-choice question answering`: çoktan seçmeli soru cevaplama
  - `LLM-as-a-judge`: LLM'yi yargıç olarak kullanma
- `However, models are commonly integrated into broader systems that provide additional context`: Ancak, modeller genellikle ek bağlam sağlayan daha geniş sistemlere entegre edilir.
  - `additional context`: ek bağlam
- `We introduced two evaluation frameworks for RAG systems, Ragas and ARES`: RAG sistemleri için iki değerlendirme çerçevesi tanıttık: Ragas ve ARES.
  - `RAG systems`: RAG sistemleri
  - `Ragas` ve `ARES`: RAG sistemleri için değerlendirme çerçeveleri
- `We saw both similarities and differences in how they evaluate RAG systems`: Hem benzerlikleri hem de RAG sistemlerini nasıl değerlendirdiklerindeki farklılıkları gördük.
  - `synthetic data generation`: sentetik veri üretimi
  - `context-based metrics`: bağlam-tabanlı ölçümler
  - `trained classifiers`: eğitilmiş sınıflandırıcılar
- `Finally, we evaluated TwinLlama-3.1-8B with a judge LLM according to three criteria`: Son olarak, TwinLlama-3.1-8B'yi bir yargıç LLM ile üç kritere göre değerlendirdik.
  - `TwinLlama-3.1-8B`: Değerlendirilen model
  - `judge LLM`: Yargıç LLM
  - `relevance`: alaka
  - `coherence`: tutarlılık
  - `conciseness`: özülük
- `In the next chapter, we will explore inference optimization techniques`: Bir sonraki bölümde, çıkarım optimizasyonu tekniklerini inceleyeceğiz.
  - `inference optimization`: çıkarım optimizasyonu
- `to improve speed and reduce memory usage, without significantly compromising model performance`: model performansından önemli ölçüde ödün vermeden hızı artırmak ve bellek kullanımını azaltmak için.
  - `model performance`: model performansı
- `We will also delve into optimization methods, model parallelism techniques and examine different quantization approaches`: Ayrıca, optimizasyon yöntemlerini, model paralelliği tekniklerini ve farklı niceleme yaklaşımlarını inceleyeceğiz.
  - `optimization methods`: optimizasyon yöntemleri
  - `model parallelism`: model paralelliği
  - `quantization approaches`: niceleme yaklaşımları

---

