# Direct Preference Optimization: Your Language Model is Secretly a Reward Model 
Rafailov ve diğerleri tarafından 2023 tarihli Direct Preference Optimization: Your Language Model is Secretly a Reward Model makalesinde tanıtılan DPO, geleneksel RLHF (Reinforcement Learning from Human Feedback) yöntemlerine göre daha basit bir alternatif sunar. DPO'nun temel yeniliği, tercih öğrenme (preference learning) probleminin yeniden formüle edilmesinde yatmaktadır. 

Geleneksel RLHF yöntemlerinin aksine, ayrı bir ödül modeli (reward model) eğitip daha sonra PPO (Proximal Policy Optimization) gibi takviyeli öğrenme (reinforcement learning) algoritmaları kullanarak dil modelini (language model) fine-tune etmek yerine, DPO daha doğrudan bir yaklaşım izler. 
DPO, bir referans politikası (reference policy) ile KL-divergence (Kullback-Leibler divergence) kısıtlaması altında beklenen ödülü (expected reward) maksimize etme standardı RLHF objektifine göre optimal politikanın (optimal policy) kapalı formdaki ifadesini (closed-form expression) türetir. 
Bu matematiksel içgörü, DPO'nun tercih öğrenme problemini doğrudan politika (policy) açısından ifade etmesini sağlar, böylece ayrı bir ödül modeline veya karmaşık takviyeli öğrenme algoritmalarına olan ihtiyacı ortadan kaldırır.

Pratik açıdan, DPO, dil modelinin çıktı olasılıkları (output probabilities) üzerinde doğrudan çalışan basit bir ikili çapraz entropi kaybı (binary cross-entropy loss) fonksiyonu olarak uygulanabilir. 
Bu kayıp fonksiyonu, modelin tercih edilen yanıtlara (preferred responses) daha yüksek olasılık, tercih edilmeyen yanıtlara (non-preferred responses) ise daha düşük olasılık atamasını teşvik ederken, bir referans (dondurulmuş) modele (reference model) yakın kalmayı sağlar. 
Referans modelin önemi, 0 ile 1 arasındaki bir beta parametresi (beta parameter) aracılığıyla doğrudan kontrol edilir. 
Beta 0'a eşit olduğunda referans model yok sayılır, bu da eğitilen modelin SFT (Supervised Fine-Tuning) modelinden çok farklı olabileceği anlamına gelir. 
Pratikte, 0.1 değeri en popüler olanıdır, ancak bu değer bir sonraki bölümde göreceğimiz gibi ayarlanabilir.

Bu yaklaşımın basitliği, eğitim sırasında modelden örnekleme (sampling) yapmaya veya karmaşık RL algoritmaları uygulamaya gerek kalmadan standart gradyan iniş (gradient descent) teknikleri kullanılarak optimizasyon yapılmasını sağlar. 
Şekil 6.5, DPO algoritmasının üst düzey bir görünümünü gösterir ve Şekil 6.4'e kıyasla eğitim sürecini büyük ölçüde basitleştirir.

# DPO Algoritmasının Avantajları
DPO'nun geleneksel RLHF yöntemlerine göre çeşitli avantajları vardır. 
Daha önce de belirtildiği gibi, tercih öğrenme ardışık düzenini (preference learning pipeline) önemli ölçüde basitleştirir, RLHF yöntemleriyle ilişkili mühendislik karmaşıklığını (engineering complexity) azaltır. 
Ayrı bir ödül modeline ve RL algoritmalarına olan ihtiyacı ortadan kaldırarak, DPO, geleneksel RLHF yaklaşımlarına göre daha hesaplamalı olarak verimlidir (computationally efficient). 
Özellikle adapterlarla (LoRA, QLoRA) eğitildiğinde, dondurulmuş ve eğitilen modellerin ayrılmasına gerek yoktur. 
Aslında, sadece adapterları eğittiğimiz için eğitilen model değiştirilmez. 
Bu, iki model yerine sadece bir model yüklememizi sağlar, bu da ek VRAM (Video Random Access Memory) tasarrufu sağlar.

Basitliğine rağmen, DPO genellikle daha karmaşık RLHF yöntemlerinin performansıyla eşleşir. 
Ayrıca, eğitim sırasında daha kararlı olma ve hiperparametrelere (hyperparameters) daha az duyarlı olma eğilimindedir. 
Basitleştirilmiş yaklaşım, DPO'yu, özellikle kapsamlı RL bilgisi olmayan küçük takımlar için daha kolay uygulama ve ölçeklendirme yapar.

RLHF, çoklu eğitim turları (training rounds) aracılığıyla iteratif iyileştirmeye ve yeni tercihlere dinamik olarak uyum sağlamaya izin verirken, DPO benzer sonuçlara ulaşmak için daha basit bir yol sunar. 
DPO ve PPO tabanlı RLHF arasındaki seçim, genellikle uygulama kolaylığı ile potansiyel zirve performansı arasındaki bir dengeye dayanır. 
Milyonlarca tercih örneği (preference sample) içeren büyük ölçekli eğitim çalıştırmaları (large-scale training runs) için, PPO esinli yöntemler hala daha yüksek bir performans tavanına sahiptir. 
Ancak, çoğu uygulama için DPO, daha düşük bir hesaplama ve mühendislik maliyetiyle performans faydalarının çoğunu sağlar.

Hem RLHF hem de DPO, sentetik verilerin (synthetic data) entegrasyonundan önemli ölçüde yararlanır. 
LLM'ler (Large Language Models) daha yetenekli hale geldikçe, insan tarafından oluşturulan içeriği kalite ve çeşitlilik açısından aşan veri üretebilirler. 
Bu, daha iyi modellerin daha iyi eğitim verileri ürettiği, bu da sırasıyla model iyileştirmelerine yol açan bir erdemli döngüyü (virtuous cycle) mümkün kılar. 
Her iki yaklaşımın da iteratif doğası, model performansının farklı yönlerine odaklanan ve çeşitli alanlardaki yetenekleri kademeli olarak geliştiren çoklu model iyileştirme turlarına izin verir.

# DPO'nun Dezavantajları
DPO'nun avantajlarına rağmen, bazı dezavantajları da vardır. 
RLHF gibi, DPO da eşleştirilmiş tercih verileri (paired preference data) gerektirir, bu da pahalı ve zaman alıcı olabilir. 
DPO, takviyeli öğrenme yaklaşımlarıyla ilişkili bazı teorik garantilerden (theoretical guarantees) yoksundur. 
RLHF'nin ek esnekliğinin faydalı olduğu senaryolar olabilir, özellikle karmaşık görevler veya ortamlar için. 
Yine de, DPO çoğu durumda idealdir, ikiz LLM örneğimiz de dahil.

Aşağıdaki kod bloğu, yukarıdaki metinde bahsi geçen kavramları içermemektedir, bunun yerine kod açıklamaları yapılacaktır.

Kod Bloğu:
```python
# DPO Loss Function
def dpo_loss(logits, reference_logits, beta, labels):
    # Calculate the probability of the preferred and non-preferred responses
    preferred_probs = torch.nn.functional.softmax(logits, dim=-1)
    non_preferred_probs = torch.nn.functional.softmax(reference_logits, dim=-1)

    # Calculate the loss
    loss = -torch.log(preferred_probs[:, labels == 1]) - beta * torch.log(non_preferred_probs[:, labels == 0])

    return loss.mean()

# Training Loop
for epoch in range(num_epochs):
    for batch in train_dataloader:
        # Zero the gradients
        optimizer.zero_grad()

        # Forward pass
        outputs = model(batch['input_ids'])
        logits = outputs.logits

        # Calculate the reference logits
        with torch.no_grad():
            reference_outputs = reference_model(batch['input_ids'])
            reference_logits = reference_outputs.logits

        # Calculate the loss
        loss = dpo_loss(logits, reference_logits, beta, batch['labels'])

        # Backward pass
        loss.backward()

        # Update the model parameters
        optimizer.step()
```

Kod Açıklaması:
1. `dpo_loss` fonksiyonu, DPO kaybını hesaplar. 
   - `logits` ve `reference_logits`, sırasıyla eğitilen model ve referans modelin çıktı logaritmik olasılıklarıdır (logits).
   - `beta`, referans modelin önemini kontrol eden hiperparametredir.
   - `labels`, tercih edilen ve tercih edilmeyen yanıtları gösteren etiketlerdir.

2. Fonksiyon içinde, önce `preferred_probs` ve `non_preferred_probs` hesaplanır. 
   - `preferred_probs`, eğitilen modelin çıktı olasılıklarıdır (output probabilities).
   - `non_preferred_probs`, referans modelin çıktı olasılıklarıdır.

3. Daha sonra, kayıp (`loss`) hesaplanır. 
   - Tercih edilen yanıtlar için (`labels == 1`), kaybın ilk terimi `-torch.log(preferred_probs)` şeklinde hesaplanır.
   - Tercih edilmeyen yanıtlar için (`labels == 0`), kaybın ikinci terimi `-beta * torch.log(non_preferred_probs)` şeklinde hesaplanır.

4. Eğitim döngüsünde (`Training Loop`), her bir epoch ve her bir batch için:
   - Gradyanlar sıfırlanır (`optimizer.zero_grad()`).
   - İleri geçiş (`forward pass`) yapılır ve `logits` elde edilir.
   - Referans modelin çıktı logaritmik olasılıkları (`reference_logits`) hesaplanır.
   - DPO kaybı (`dpo_loss`) hesaplanır.
   - Geri geçiş (`backward pass`) yapılır ve model parametreleri güncellenir (`optimizer.step()`).

Bu kod, DPO algoritmasının temel adımlarını gösterir ve basit bir şekilde nasıl uygulanabileceğini gösterir.

---

