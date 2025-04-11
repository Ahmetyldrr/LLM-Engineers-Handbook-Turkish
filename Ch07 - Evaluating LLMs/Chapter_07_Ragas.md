# Retrieval-Augmented Generation Assessment (Ragas) Değerlendirme Çerçevesi

Retrieval-Augmented Generation Assessment (Ragas), geliştiricilere RAG değerlendirmesi ve optimizasyonu için kapsamlı bir araç seti sağlamak üzere tasarlanmış açık kaynaklı bir araç takımıdır. Veri odaklı kararlar almak için metriklerin sürekli olarak izlenmesini içeren metrik odaklı geliştirme (Metrics-Driven Development - MDD) yaklaşımına dayanmaktadır. Bu metodolojiyi benimseyerek, Ragas geliştiricilerin RAG sistemlerini objektif olarak değerlendirmelerine, iyileştirme alanlarını belirlemelerine ve zaman içinde değişikliklerin etkisini izlemelerine olanak tanır.

Ragas'ın temel yeteneklerinden biri, çeşitli ve karmaşık test veri kümelerini sentetik olarak oluşturabilmesidir. Bu özellik, RAG geliştirmede önemli bir sorun olan yüzlerce soru, cevap ve içerik oluşturmanın zaman alıcı ve emek yoğun olmasını ele alır. Bunun yerine, Evol-Instruct gibi çalışmaların evrimsel yaklaşım paradigmasını kullanarak, çeşitli özelliklere sahip (örneğin, akıl yürütme karmaşıklığı, koşullu elemanlar ve çoklu içerik gereksinimleri gibi) sorular oluşturur. Bu yaklaşım, RAG hattındaki farklı bileşenlerin kapsamlı bir şekilde değerlendirilmesini sağlar. Ayrıca, Ragas sohbet tabanlı soru-cevap etkileşimlerini simüle eden konuşma örnekleri oluşturabilir, böylece geliştiricilerin sistemlerini daha gerçekçi senaryolarda değerlendirmelerine olanak tanır.

# Şekil 7.1: Ragas Değerlendirme Çerçevesinin Genel Görünümü

Şekil 7.1'de gösterildiği gibi, Ragas RAG sistem performansının farklı yönlerini objektif olarak ölçmek için tasarlanmış bir dizi LLM destekli değerlendirme metriği sağlar. Bu metrikler şunları içerir:

*   **Faithfulness (Dogruluk)**: Bu metrik, oluşturulan cevabın verilen içerikle gerçeklik tutarlılığını ölçer. Cevabı bireysel iddialara ayırarak ve her bir iddianın verilen içerikten çıkarılıp çıkarılamayacağını doğrular. Doğruluk puanı, doğrulanabilir iddiaların cevap içindeki toplam iddialara oranı olarak hesaplanır.
    ```python
# Örnek kod
def faithfulness_score(answer, context):
    claims = break_down_answer(answer)  # Cevabı iddialara ayır
    verifiable_claims = verify_claims(claims, context)  # İddiaları içerikle doğrula
    return len(verifiable_claims) / len(claims)  # Doğruluk puanını hesapla
```
    *   `break_down_answer(answer)`: Cevabı bireysel iddialara ayırır.
    *   `verify_claims(claims, context)`: Her bir iddianın verilen içerikten çıkarılıp çıkarılamayacağını kontrol eder.
    *   `len(verifiable_claims) / len(claims)`: Doğruluk puanını hesaplar.
*   **Answer Relevancy (Cevap Uygunluğu)**: Bu metrik, oluşturulan cevabın verilen soru ile ne kadar ilgili olduğunu değerlendirir. Cevaba dayanarak birden fazla soru oluşturmak için bir LLM kullanır ve daha sonra oluşturulan bu sorular ile orijinal soru arasındaki ortalama kosinüs benzerliğini hesaplar. Bu yöntem, gerçekte doğru ancak konu dışı veya eksik olan cevapları belirlemeye yardımcı olur.
    ```python
# Örnek kod
def answer_relevancy_score(answer, question):
    generated_questions = generate_questions(answer)  # Cevaba dayanarak sorular oluştur
    similarities = calculate_cosine_similarity(generated_questions, question)  # Kosinüs benzerliğini hesapla
    return mean(similarities)  # Ortalama kosinüs benzerliğini döndür
```
    *   `generate_questions(answer)`: Cevaba dayanarak birden fazla soru oluşturur.
    *   `calculate_cosine_similarity(generated_questions, question)`: Oluşturulan sorular ile orijinal soru arasındaki kosinüs benzerliğini hesaplar.
    *   `mean(similarities)`: Ortalama kosinüs benzerliğini hesaplar.
*   **Context Precision (İçerik Hassasiyeti)**: Bu metrik, içeriklerde bulunan tüm gerçek ilgili öğelerin uygun şekilde sıralanıp sıralanmadığını değerlendirir. İlgili bilginin içerik içindeki konumunu dikkate alır ve en ilgili bilgiyi üst sıralara koyan sistemleri ödüllendirir.
    ```python
# Örnek kod
def context_precision_score(context, relevant_items):
    ranked_items = rank_items(context)  # İçerikteki öğeleri sırala
    relevant_ranked_items = [item for item in ranked_items if item in relevant_items]  # İlgili öğeleri belirle
    return calculate_precision(relevant_ranked_items)  # Hassasiyeti hesapla
```
    *   `rank_items(context)`: İçerikteki öğeleri sıralar.
    *   `[item for item in ranked_items if item in relevant_items]`: İlgili öğeleri belirler.
    *   `calculate_precision(relevant_ranked_items)`: Hassasiyeti hesaplar.
*   **Context Recall (İçerik Hatırlama):** Bu metrik, alınan içerik ile gerçek cevap arasındaki uyumu ölçer. Gerçek cevaptaki her bir iddiayı analiz ederek, alınan içerikle ilişkilendirip ilişkilendirilemeyeceğini belirler ve alınan bilginin eksiksizliği hakkında fikir verir.
    ```python
# Örnek kod
def context_recall_score(context, ground_truth_answer):
    claims = break_down_answer(ground_truth_answer)  # Gerçek cevabı iddialara ayır
    attributable_claims = attribute_claims(claims, context)  # İddiaları içerikle ilişkilendir
    return len(attributable_claims) / len(claims)  # Hatırlama oranını hesapla
```
    *   `break_down_answer(ground_truth_answer)`: Gerçek cevabı iddialara ayırır.
    *   `attribute_claims(claims, context)`: İddiaları içerikle ilişkilendirir.
    *   `len(attributable_claims) / len(claims)`: Hatırlama oranını hesaplar.

Son olarak, Ragas üretim ortamlarında RAG kalitesini izlemek için yapı taşları sağlar. Bu, RAG sistemlerinin sürekli olarak iyileştirilmesini kolaylaştırır. Test veri kümelerinden elde edilen değerlendirme sonuçlarından ve üretim izlemesinden elde edilen içgörülerden yararlanarak, geliştiriciler uygulamalarını iteratif olarak geliştirebilirler. Bu, alma algoritmalarını ince ayar yapmayı, soru mühendisliği stratejilerini ayarlamayı veya alınan içerik ile LLM oluşturma arasındaki dengeyi optimize etmeyi içerebilir. Ragas, özel sınıflandırıcılara dayanan başka bir yaklaşımla tamamlanabilir.

```python
# Ragas'ın temel yeteneklerini birleştiren örnek bir kod
def ragas_evaluation(answer, context, question, ground_truth_answer, relevant_items):
    faithfulness = faithfulness_score(answer, context)
    answer_relevancy = answer_relevancy_score(answer, question)
    context_precision = context_precision_score(context, relevant_items)
    context_recall = context_recall_score(context, ground_truth_answer)
    
    return {
        "Faithfulness": faithfulness,
        "Answer Relevancy": answer_relevancy,
        "Context Precision": context_precision,
        "Context Recall": context_recall
    }

# Örnek kullanım
answer = "Örnek cevap"
context = "Örnek içerik"
question = "Örnek soru"
ground_truth_answer = "Gerçek cevap"
relevant_items = ["İlgili öğe 1", "İlgili öğe 2"]

evaluation_results = ragas_evaluation(answer, context, question, ground_truth_answer, relevant_items)
print(evaluation_results)
```

Bu kod, Ragas'ın farklı değerlendirme metriklerini bir araya getirerek kapsamlı bir değerlendirme sağlar. Her bir metrik, RAG sisteminin farklı yönlerini değerlendirir ve iyileştirme alanlarını belirlemeye yardımcı olur.

---

