#MLOps Çekirdek Bileşenleri
Bu bileşenlerin tümünü kitabın her yerinde kullandık, ancak şimdi MLOps çekirdek bileşenleri hakkında hızlı bir hatırlatma yapalım. Kaynak kontrolü (source control) ve CI/CD ile birlikte, MLOps aşağıdaki bileşenler etrafında dönmektedir:
- Model kaydı (Model registry): Eğitilmiş makine öğrenimi (ML) modellerini depolamak için merkezi bir depo ( araçlar: Comet ML, W&B, MLflow, ZenML )
- Özellik deposu (Feature store): Hem model eğitimi hem de çıkarım (inference) ardışık düzenleri (pipelines) için girdi verilerini özellikler olarak ön işleme (preprocessing) ve depolama ( araçlar: Hopsworks, Tecton, Featureform )
- ML meta veri deposu (ML metadata store): Bu depo, model konfigürasyonları, eğitim verileri, test verileri ve performans metrikleri (performance metrics) gibi model eğitimi ile ilgili bilgileri izler. Esas olarak birden fazla modeli karşılaştırmak ve model soylarını (model lineages) inceleyerek nasıl oluşturulduklarını anlamak için kullanılır ( araçlar: Comet ML, W&B, MLflow )
- ML ardışık düzen düzenleyicisi (ML pipeline orchestrator): Makine öğrenimi projelerindeki adımların sırasını otomatize eder ( araçlar: ZenML, Airflow, Prefect, Dagster )

MLOps bileşenleri ile spesifik araçları arasındaki örtüşmeyi fark etmiş olabilirsiniz. Bu yaygındır, çünkü çoğu MLOps aracı, genellikle MLOps platformları olarak adlandırılan birleşik çözümler sunar.

**Kod Açıklaması:**

Yukarıdaki metinde kod bulunmamaktadır. Ancak aşağıdaki gibi bir kod örneği verilebilir:

Örneğin, aşağıdaki Python kodunu ele alalım:
```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score

# Veri yükleme
df = pd.read_csv('data.csv')

# Veri ön işleme
X = df.drop('target', axis=1)
y = df['target']

# Eğitim ve test verilerini ayırma
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Model eğitimi
model = RandomForestClassifier(n_estimators=100)
model.fit(X_train, y_train)

# Model değerlendirmesi
y_pred = model.predict(X_test)
accuracy = accuracy_score(y_test, y_pred)
print(f'Accuracy: {accuracy:.3f}')
```
Bu kodun satır satır açıklaması:
1. `import pandas as pd`: Pandas kütüphanesini içe aktarır ve `pd` takma adını verir.
2. `from sklearn.model_selection import train_test_split`: Scikit-learn kütüphanesinden `train_test_split` fonksiyonunu içe aktarır.
3. `from sklearn.ensemble import RandomForestClassifier`: Scikit-learn kütüphanesinden `RandomForestClassifier` sınıfını içe aktarır.
4. `from sklearn.metrics import accuracy_score`: Scikit-learn kütüphanesinden `accuracy_score` fonksiyonunu içe aktarır.
5. `df = pd.read_csv('data.csv')`: `data.csv` dosyasını okur ve `df` değişkenine atar.
6. `X = df.drop('target', axis=1)`: `target` sütununu düşürür ve kalan sütunları `X` değişkenine atar.
7. `y = df['target']`: `target` sütununu `y` değişkenine atar.
8. `X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)`: Eğitim ve test verilerini ayırır.
9. `model = RandomForestClassifier(n_estimators=100)`: `RandomForestClassifier` modelini oluşturur.
10. `model.fit(X_train, y_train)`: Modeli eğitir.
11. `y_pred = model.predict(X_test)`: Test verilerini kullanarak tahmin yapar.
12. `accuracy = accuracy_score(y_test, y_pred)`: Doğruluk skorunu hesaplar.
13. `print(f'Accuracy: {accuracy:.3f}')`: Doğruluk skorunu yazdırır.

İngilizce teknik terimlerin Türkçe karşılıkları parantez içinde verilmiştir:
- Model registry (Model kaydı)
- Feature store (Özellik deposu)
- ML metadata store (ML meta veri deposu)
- ML pipeline orchestrator (ML ardışık düzen düzenleyicisi)
- Source control (Kaynak kontrolü)
- CI/CD (Sürekli entegrasyon/sürekli teslimat)
- Preprocessing (Ön işleme)
- Inference (Çıkarım)
- Pipelines (Ardışık düzenler)
- Performance metrics (Performans metrikleri)
- Model lineages (Model soyları)

---

