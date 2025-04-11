#SageMaker Çıkarım Uç Noktaları için Otomatik Ölçeklendirme Kurulumu

SageMaker Çıkarım (Inference) uç noktalarınız için otomatik ölçeklendirme (autoscaling) kurarken, ölçeklendirme ilkenizi (scaling policy) oluşturmadan önce maksimum ve minimum ölçeklendirme sınırlarınızı belirlemek çok önemlidir. Minimum değer, modelinizin çalışabileceği en az kaynakları (resources) temsil eder. Bu değerin en az 1 olması gerekir, böylece modeliniz her zaman bazı kapasitelere sahip olur. Ardından, modelinizin ölçeklenebileceği üst kaynak sınırını tanımlayan maksimum değeri yapılandırın. Maksimum değer, minimum değerden büyük veya ona eşit olmalıdır, ancak herhangi bir üst sınır getirmez. Böylece, AWS'nin sağlayabileceği sınırlar içinde uygulamanızın ihtiyaç duyduğu kadar ölçeklendirme yapabilirsiniz.

İngilizce Teknik Terimler Parantez İçinde:
SageMaker Çıkarım (Inference) uç noktalarınız için otomatik ölçeklendirme (autoscaling) kurarken, ölçeklendirme ilkenizi (scaling policy) oluşturmadan önce maksimum ve minimum ölçeklendirme sınırlarınızı (scaling limits) belirlemek çok önemlidir. Minimum değer, modelinizin çalışabileceği en az kaynakları (resources) temsil eder. Bu değerin en az 1 olması gerekir, böylece modeliniz her zaman bazı kapasitelere (capacity) sahip olur. Ardından, modelinizin ölçeklenebileceği üst kaynak sınırını tanımlayan maksimum değeri yapılandırın (configure). Maksimum değer, minimum değerden büyük veya ona eşit olmalıdır (>=), ancak herhangi bir üst sınır getirmez. Böylece, AWS'nin sağlayabileceği sınırlar içinde uygulamanızın ihtiyaç duyduğu kadar ölçeklendirme yapabilirsiniz.

Kodların Ayrıntılı Açıklaması:
Paragraf içinde kod bulunmamaktadır, ancak bazı teknik terimlerin açıklamaları aşağıda verilmiştir:

- `SageMaker`: Amazon SageMaker, makine öğrenimi modellerini oluşturmak, eğitmek ve dağıtmak için kullanılan bir AWS hizmetidir.
- `Inference`: Çıkarım, eğitilmiş bir makine öğrenimi modelinin yeni veriler üzerinde tahminler yapması işlemidir.
- `Autoscaling`: Otomatik ölçeklendirme, uygulamanın talebine göre kaynakların otomatik olarak artırılması veya azaltılması işlemidir.
- `Scaling policy`: Ölçeklendirme ilkesi, otomatik ölçeklendirme için kaynakların ne zaman ve nasıl artırılacağını veya azaltılacağını belirleyen kurallardır.
- `Resources`: Kaynaklar, bir uygulamanın çalışması için gereken hesaplama, bellek ve diğer bileşenlerdir.
- `Capacity`: Kapasite, bir uygulamanın veya sisteminin işleyebileceği maksimum yük veya işlem miktarıdır.

Ayrıca, aşağıdaki gibi kod örnekleri olsaydı, açıklamaları şöyle olurdu:
```python
# Örnek Kod
min_scaling_limit = 1  # Minimum ölçeklendirme sınırı
max_scaling_limit = 10  # Maksimum ölçeklendirme sınırı

# Ölçeklendirme ilkesini yapılandırma
scaling_policy = {
    'min_capacity': min_scaling_limit,
    'max_capacity': max_scaling_limit
}
```
- `min_scaling_limit = 1`: Minimum ölçeklendirme sınırını 1 olarak ayarlar.
- `max_scaling_limit = 10`: Maksimum ölçeklendirme sınırını 10 olarak ayarlar.
- `scaling_policy`: Ölçeklendirme ilkesini tanımlayan bir sözlük (dictionary) oluşturur.
- `'min_capacity': min_scaling_limit`: Ölçeklendirme ilkesinin minimum kapasitesini `min_scaling_limit` değişkenine atar.
- `'max_capacity': max_scaling_limit`: Ölçeklendirme ilkesinin maksimum kapasitesini `max_scaling_limit` değişkenine atar.

---

