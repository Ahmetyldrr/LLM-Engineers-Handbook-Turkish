# Embeddings ile Makine Öğrenimi (ML) Modellerinde Kategorik Değişkenlerin Kodlanması

Öncelikle, ML modelleri yalnızca sayısal değerlerle çalışır. Bu, tabular verilerle çalışırken bir sorun değildir, çünkü veriler genellikle sayısal biçimdedir veya kolayca sayılara işlenebilir. Kelimeleri, resimleri veya ses verilerini modellere beslemek istediğimizde embeddings kullanışlı olur. Örneğin, transformer modelleriyle çalışırken, tüm metin girişinizi tokenize edersiniz, burada her tokenin bir embeddingi vardır. Bu sürecin güzelliği basitliğinde yatar; transformer giriş, kolayca ve güvenle sinir ağının yoğun katmanları tarafından yorumlanabilen bir embedding dizisidir. Bu örneğe dayanarak, herhangi bir kategorik değişkeni kodlamak ve bir ML modeline beslemek için embeddings kullanabilirsiniz. Peki neden one-hot encoding gibi diğer basit yöntemleri kullanmıyorsunuz? Yüksek kardinaliteye sahip kategorik değişkenlerle çalışırken, diğer klasik yöntemleri kullanırken boyutluluk lanetinden (curse of dimensionality) muzdarip olacaksınız. Örneğin, eğer kelime hazneniz 10.000 token içeriyorsa, one-hot encoding uyguladıktan sonra yalnızca bir tokenin uzunluğu 10.000 olacaktır. Giriş dizisi N token içeriyorsa, bu N * 10.000 giriş parametresi haline gelecektir. Eğer N >= 100 ise, genellikle metin girişi yaparken, giriş çok büyük olduğu için kullanılamaz. Diğer klasik yöntemlerle ilgili bir başka sorun da, hashing gibi boyutluluk lanetinden mustarip olmayan yöntemlerin vektörler arasındaki anlamsal ilişkileri kaybetmesidir.

# One-Hot Encoding ve Feature Hashing

One-hot encoding, kategorik değişkenleri ikili matris gösterimine dönüştüren bir tekniktir (binary matrix representation). Her kategori benzersiz bir ikili vektör olarak temsil edilir. Her kategorik değişken için, uzunluğu benzersiz kategorilerin sayısına eşit olan bir ikili vektör oluşturulur, burada tüm değerler sıfırdır, belirli bir kategoriye karşılık gelen indeks hariç, bu indeks bir olarak ayarlanır. Yöntem, kategoriler hakkındaki tüm bilgileri korur. Basit ve yorumlanabilir. Ancak, önemli bir dezavantajı, eğer kategorik değişken birçok benzersiz değere sahipse yüksek boyutlu bir özellik uzayına (feature space) yol açabilmesidir, bu da yöntemi pratik olmayan hale getirir.

Feature hashing, aynı zamanda hashing encoding veya "hash trick" olarak bilinir, kategorik değişkenleri sayısal özelliklere dönüştürmek için bir hash fonksiyonu uygulayarak kullanılan bir tekniktir. One-hot encoding ile karşılaştırıldığında, yöntem benzersiz kategorilerin sayısına bağlı değildir, ancak kategorileri sabit sayıda bölmeye veya kovaya eşleyerek özellik uzayının boyutluluğunu azaltır. Böylece, özellikle yüksek kardinaliteye sahip kategorik değişkenlerle uğraşırken yararlı olan özellik uzayının boyutluluğunu azaltır. Bu, hafıza kullanımı ve hesaplama zamanı açısından verimlidir. Ancak, farklı kategorilerin aynı bölmeye eşlenmesine neden olan çarpışmalar riski vardır, bu da bilgi kaybına yol açar. Eşleme, yöntemi yorumlanamaz hale getirir. Ayrıca, orijinal kategoriler ve hashlenmiş özellikler arasındaki ilişkiyi anlamak zordur.

# Embeddings ile Kategorik Değişkenlerin Kodlanması

Embeddings, çıktı vektörünün boyutunu kontrol ederken kategorik değişkenleri kodlamamıza yardımcı olur. Ayrıca, saf hashing hilelerinden daha düşük boyutlu bir uzaya bilgileri yoğunlaştırmanın ustaca yollarını kullanırlar. İkinci olarak, girişinizi embeddinglemek, boyutunun boyutunu azaltır ve tüm anlamsal anlamını yoğun bir vektöre yoğunlaştırır. Bu, özellikle resimlerle çalışırken son derece popüler bir tekniktir, burada bir CNN encoder modülü yüksek boyutlu anlamı bir embeddinge eşler, daha sonra bir CNN decoder tarafından sınıflandırma veya regresyon adımlarını gerçekleştirerek işlenir.

Aşağıdaki resim tipik bir CNN düzenini gösterir. Her katmandaki küçük kareleri hayal edin. Bunlar "alıcı alanlardır" (receptive fields). Her kare, önceki katmandaki tek bir nörona bilgi besler. Ağ boyunca ilerledikçe, iki önemli şey olur: 
- Resmin küçülmesi: Özel "altörnekleme" (subsampling) işlemleri, katmanları küçültür, temel detaylara odaklanır.
- Özelliklerin öğrenilmesi: "Evrişim" (convolution) işlemleri, ağ daha karmaşık özellikler öğrendiğinde katman boyutunu artırır.

Son olarak, sonundaki tam bağlı katman (fully connected layer), tüm bu işlenmiş bilgiyi alır ve son vektör embeddingine, sayısal bir resim gösterimine dönüştürür.

```python
# Örnek Kod: Embedding Kullanımı
import numpy as np
from tensorflow.keras.layers import Embedding

# Kategorik değişkenlerin sayısal gösterimi için embedding katmanı
embedding_layer = Embedding(input_dim=10000, output_dim=128, input_length=100)

# Örnek girdi
input_data = np.random.randint(0, 10000, size=(32, 100))

# Embedding katmanından geçirme
output = embedding_layer(input_data)

print(output.shape)
```

Kod Açıklaması:
1. `import numpy as np`: Numpy kütüphanesini içe aktarır, sayısal işlemler için kullanılır.
2. `from tensorflow.keras.layers import Embedding`: TensorFlow'un Keras API'sinden Embedding katmanını içe aktarır.
3. `embedding_layer = Embedding(input_dim=10000, output_dim=128, input_length=100)`: 
   - `input_dim=10000`: Giriş verisinin maksimum indeksini belirtir, yani 10000 farklı kategorik değer olabilir.
   - `output_dim=128`: Her bir kategorik değerin embedding vektörünün boyutunu belirtir, yani her kategori 128 boyutlu bir vektörle temsil edilir.
   - `input_length=100`: Giriş dizisinin uzunluğunu belirtir, yani her örnek 100 kategorik değer içerir.
4. `input_data = np.random.randint(0, 10000, size=(32, 100))`: 
   - 32 örnek içeren ve her örnekte 100 kategorik değer olan rastgele bir numpy dizisi oluşturur. Değerler 0 ile 10000 arasında rastgele tam sayılardır.
5. `output = embedding_layer(input_data)`: Oluşturulan `input_data`'yı embedding katmanından geçirir.
6. `print(output.shape)`: Embedding katmanından çıkan sonucun şeklini yazdırır. Boyut `(32, 100, 128)` olmalıdır, yani 32 örnek, her örnekte 100 kategorik değer ve her değer 128 boyutlu bir vektörle temsil ediliyor.

---

