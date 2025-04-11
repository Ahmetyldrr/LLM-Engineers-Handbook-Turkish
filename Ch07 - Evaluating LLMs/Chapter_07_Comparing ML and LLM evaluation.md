#ML Değerlendirmesi (ML Evaluation)

ML değerlendirmesi, tahmin, sınıflandırma ve regresyon gibi görevler için tasarlanan modellerin performansını değerlendirmeye odaklanır. LLM'lerin (Large Language Models) değerlendirilmesinin aksine, genellikle bir modelin dili ne kadar iyi anladığı ve ürettiği üzerinde durulur, ML değerlendirmesi daha çok bir modelin yapılandırılmış verileri işleyerek belirli sonuçlar üretmede ne kadar doğru ve verimli olduğu ile ilgilenir. Bu fark, bu modellerin ele aldığı görevlerin doğasından kaynaklanır. ML modelleri genellikle dar tanımlı problemler için tasarlanır, örneğin hisse senedi fiyatlarını tahmin etmek veya aykırı değerleri tespit etmek gibi, bu da genellikle sayısal veya kategorik verileri içerir ve değerlendirme sürecini daha basit hale getirir. Diğer taraftan, LLM'ler dili yorumlama ve üretme göreviyle karşı karşıyadır, bu da değerlendirme sürecine bir öznellik katmanı ekler. Sadece sayısal ölçütlere güvenmek yerine, LLM değerlendirmesi daha incelikli bir yaklaşım gerektirir ve genellikle nitel değerlendirmeleri içerir, modelin doğal dilde tutarlı, ilgili ve bağlamsal olarak doğru yanıtlar üretme becerisini inceler.

Özellikle, bu modellerin nasıl çalıştıklarındaki üç temel farkı görebiliriz, bu da değerlendirme sürecini etkiler:
 
#Sayısal Ölçütler (Numerical Metrics)
ML modellerini değerlendirmek genellikle amaçlanan performans ölçütlerini ölçmeyi içerir, örneğin doğruluk (accuracy), kesinlik (precision), geri çağırma (recall) veya ortalama kare hatası (mean squared error) gibi, eldeki görevin türüne bağlı olarak. Bu, birden fazla görevi yerine getirebilen (dolayısıyla birden fazla değerlendirme) ve nadiren aynı sayısal ölçütlere güvenebilen LLM'ler için daha az nettir.

```markdown
### Sayısal Ölçütler (Numerical Metrics) Kod Açıklaması
# Değerlendirme ölçütü olarak doğruluk (accuracy) kullanılması
accuracy = (TP + TN) / (TP + TN + FP + FN)

# Burada:
# TP: Doğru Pozitif (True Positive)
# TN: Doğru Negatif (True Negative)
# FP: Yanlış Pozitif (False Positive)
# FN: Yanlış Negatif (False Negative)
```

#Özellik Mühendisliği (Feature Engineering)
Geleneksel ML'de, kritik bir parça, model eğitilmeden önce ilgili veri özelliklerini manuel olarak seçmeyi ve dönüştürmeyi içerir. Bu özellik mühendisliğinin başarısının değerlendirilmesi genellikle daha geniş model değerlendirmesinin bir parçası haline gelir. LLM'ler ise ham metin verilerini doğrudan işleyecek şekilde tasarlanmıştır, bu da manuel özellik mühendisliğine olan ihtiyacı azaltır.

```python
### Özellik Mühendisliği (Feature Engineering) Kod Örneği
from sklearn.feature_extraction.text import TfidfVectorizer

# TF-IDF vektörleştiricisi oluşturma
vectorizer = TfidfVectorizer()

# Metin verilerini vektörleştirme
X = vectorizer.fit_transform(metin_verileri)

# Burada:
# metin_verileri: İşlenecek metin verileri
# X: Vektörleştirilmiş veri
```

#Yorumlanabilirlik (Interpretability)
ML modelleriyle, bir modelin neden belirli tahminlerde veya sınıflandırmalarda bulunduğunu yorumlamak daha kolaydır ve bu yorumlanabilirlik, değerlendirmelerinin temel bir parçası olabilir. Bu doğrudan yorumlama LLM'ler için mümkün değildir. Ancak, oluşturma sürecinde açıklamalar istemek, modelin karar verme sürecine dair içgörüler sağlayabilir.

```python
### Yorumlanabilirlik (Interpretability) Kod Örneği
import shap

# SHAP (SHapley Additive exPlanations) açıklayıcısı oluşturma
explainer = shap.Explainer(model)

# Gözlem için SHAP değerlerini hesaplama
shap_values = explainer(X)

# Burada:
# model: Kullanılan makine öğrenimi modeli
# X: Gözlem verileri
# shap_values: SHAP değerleri
```

Sonraki bölümde, farklı LLM türlerinin daha ayrıntılı bir incelemesini göreceğiz. Genel amaçlı modellerin değerlendirilmesi, ML değerlendirmesinden oldukça farklıyken, görev-specifik LLM'ler geleneksel ML ile daha yakından uyumludur.

---

