# Reinforcement Learning from Human Feedback (RLHF)

İnsan Geri Bildiriminden Pekiştirmeli Öğrenme (RLHF), pekiştirmeli öğrenmeyi (RL) insan girdisiyle birleştirerek modelleri insan tercihleri ve değerleriyle uyumlu hale getirir. RLHF, geleneksel RL yöntemlerindeki zorluklara, özellikle karmaşık görevler için ödül fonksiyonlarının belirlenmesindeki zorluklara ve tasarlanan ödüller ile amaçlanan hedefler arasındaki uyumsuzluğa potansiyel bir cevap olarak ortaya çıktı. RLHF'nin kökenleri, 2011 yılında Akrour ve diğerleri ile Cheng ve diğerleri tarafından bağımsız olarak tanıtılan tercih tabanlı pekiştirmeli öğrenme (PbRL) alanına kadar uzanabilir. PbRL, nicel ödül sinyallerine güvenmek yerine, davranışlar arasındaki ikili tercihler gibi nitel geri bildirimlerden hedefleri çıkarmayı amaçladı. Bu yaklaşım, uygun ödül fonksiyonlarının tanımlanmasının zor olabileceği ve ödül hacklenmesi veya istenmeyen davranışlara eğilimli olabileceği geleneksel RL'nin bazı sınırlamalarını ele aldı. RLHF terimi daha sonra, 2021-2022 civarında, büyük dil modellerinin (LLM'ler) eğitimi bağlamında yaklaşımın öne çıkmasıyla ortaya çıktı. Ancak, temel fikirler yıllarca önce gelişmeye başlamıştı. Christiano ve diğerlerinin 2017'de yaptığı bir çalışma, insan tercihleriğinden ödül modelleri öğrenmenin ve bunları RL ajanlarını eğitmek için kullanmanın etkinliğini gösterdi. Bu çalışma, RLHF'nin elle tasarlanmış ödüllerle eğitilen ajanların performansını eşleştirebileceğini veya aşabileceğini, ancak önemli ölçüde daha az insan çabası gerektirdiğini gösterdi.

# RLHF'nin Çalışma Prensibi

RLHF, temel olarak hem bir ödül modelini hem de bir politikayı iteratif olarak geliştirerek çalışır:

## Ödül Modeli Öğrenme

Önceden tanımlanmış bir ödül fonksiyonu kullanmak yerine, RLHF insan geri bildiriminden bir ödül modeli öğrenir. Bu genellikle insanlara farklı cevaplar sunarak ve hangisini tercih ettiklerini sormak suretiyle yapılır. Bu tercihler, genellikle Bradley-Terry modeli veya tercihleri temel fayda fonksiyonlarına eşleyen benzeri yaklaşımlar kullanılarak bir ödül modelini eğitmek için kullanılır.

## Politika Optimizasyonu

Öğrenilen ödül modeliyle, standart RL algoritmaları bir politikayı optimize etmek için kullanılabilir. Bu politika, öğrenilen modelden öngörülen ödülleri maksimize etmeyi amaçlayan yeni davranışlar üretir.

## İteratif İyileştirme

Politika geliştikçe, insan tarafından değerlendirilebilecek yeni davranışlar üretir ve ödül modelinde iyileştirmelere yol açar. Bu döngü, ideal olarak insan tercihleriyle iyi uyum sağlayan bir politikayla sonuçlanır.

RLHF'deki önemli bir yenilik, insan geri bildirim maliyetini ele alma yaklaşımıdır. Sabit insan denetimi gerektirmek yerine, RLHF asenkron ve seyrek geri bildirim sağlar. Öğrenilen ödül modeli, insan tercihleri için bir vekil görevi görür ve RL algoritmasının her eylem için doğrudan insan girdisi olmadan sürekli olarak eğitilmesini sağlar.

Örneğin, Şekil 6.4, en popüler RLHF algoritmalarından biri olan Proksimal Politika Optimizasyonu (PPO) algoritmasının yüksek seviyeli bir görünümünü gösterir. Burada, ödül modeli, eğitilen model tarafından üretilen metni puanlamak için kullanılır. Bu ödül, ek bir Kullback-Leibler (KL) diverjans faktörü tarafından düzenlenir ve token dağılımının eğitimden önceki modele (dondurulmuş model) benzer kalmasını sağlar.

# PPO Algoritması

```python
# PPO algoritmasının yüksek seviyeli görünümü
def ppo_algorithm(reward_model, policy, frozen_model):
    # Ödül modelini kullanarak üretilen metni puanla
    rewards = reward_model(policy.generate_text())
    
    # Kullback-Leibler diverjans faktörü ekle
    kl_divergence = kl_divergence(policy, frozen_model)
    rewards = rewards - kl_divergence
    
    # Politikayı optimize et
    policy.optimize(rewards)
    
    return policy
```

**Kod Açıklaması:**

1. `ppo_algorithm` fonksiyonu, `reward_model`, `policy` ve `frozen_model` olmak üzere üç girdi alır.
2. `reward_model`, üretilen metni puanlamak için kullanılır.
3. `policy.generate_text()`, politika tarafından üretilen metni temsil eder.
4. `kl_divergence` fonksiyonu, politika ve dondurulmuş model arasındaki Kullback-Leibler diverjansını hesaplar.
5. `rewards` değişkeni, ödül modelinden alınan ödüllerin yanı sıra Kullback-Leibler diverjans faktörü tarafından düzenlenir.
6. `policy.optimize(rewards)`, politikayı optimize etmek için kullanılır.

RLHF, AI sistemlerini insan tercihleriyle uyumlu hale getirmede etkili olsa da, iteratif doğası ve ayrı bir ödül modeline güvenmesi nedeniyle zorluklarla karşı karşıyadır. Bu, hesaplama açısından pahalı ve potansiyel olarak kararsız olabilir. Teorik üstünlüğüne rağmen, RLHF algoritmaları deneysel olarak daha basit yaklaşımlara kıyasla daha düşük performans göstermiştir. Bu yaklaşımlardan biri olan DPO, önemli bir ilgi görmüştür.

---

