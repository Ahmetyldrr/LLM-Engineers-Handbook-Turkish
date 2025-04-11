#Veri Paralelliği (Data Parallelism)

Veri paralelliği (Data Parallelism - DP), model paralelliğinin en basit türüdür. Modelin kopyalarını oluşturmayı ve bu kopyaları farklı GPU'lara (Graphics Processing Unit) dağıtmeyi içerir (Şekil 8.4'ye bakınız). Her bir GPU, veri alt kümesini eş zamanlı olarak işler. Eğitim sırasında, her bir GPU'da hesaplanan gradyanlar (gradients) ortalaması alınır ve model parametrelerini (model parameters) güncellemek için kullanılır, böylece her bir kopyanın senkronize kalması sağlanır. Bu yaklaşım, özellikle toplu iş boyutu (batch size) tek bir makineye sığmayacak kadar büyük olduğunda veya eğitim sürecini hızlandırmayı amaçladığında oldukça faydalıdır.

Şekil 8.4 – Dört GPU ile veri paralelliğinin gösterimi

Tahmin (inference) sırasında, DP, eş zamanlı istekleri işlemek için yararlı olabilir. İş yükünü birden fazla GPU'ya dağıtarak, bu yaklaşım gecikmeyi (latency) azaltmaya yardımcı olur, çünkü birden fazla istek eş zamanlı olarak işlenebilir. Bu eş zamanlı işlem, daha yüksek sayıda istek aynı anda işlenebildiğinden verimi (throughput) de artırır. Ancak, DP'nin etkinliği model boyutuna ve GPU'lar arasındaki iletişim yüküne (communication overhead) bağlıdır. Aslında, modelin parametrelerini her bir GPU'da çoğaltmak verimsizdir. Bu, bu tekniğin yalnızca model yeterince küçük olduğunda ve tek bir GPU'ya sığdığında işe yaradığı anlamına gelir, bu da girdi verileri için daha az yer bırakır ve böylece toplu iş boyutunu sınırlar. Daha büyük modeller veya bellek kısıtlı olduğunda, bu önemli bir dezavantaj olabilir. Genellikle, DP esas olarak eğitim için kullanılırken, ardışık düzen (pipeline) ve tensör paralelliği (tensor parallelism) tahmin için tercih edilir.

**Kod Açıklaması:**

Paragrafın altında kod bulunmamaktadır, ancak metinde geçen bazı teknik terimlerin açıklamaları aşağıda verilmiştir:

- **Data Parallelism (DP)**: Veri paralelliği, modelin farklı kopyalarını farklı işlem birimlerinde çalıştırarak eğitimi hızlandırma yöntemidir.
- **GPU (Graphics Processing Unit)**: Grafik işleme birimi, yüksek performanslı hesaplama için kullanılan özel bir işlem birimidir.
- **Gradyan (Gradient)**: Bir fonksiyonun değişkenlerine göre kısmi türevlerini içeren vektördür. Eğitim sırasında modelin parametrelerini güncellemek için kullanılır.
- **Model Parametreleri (Model Parameters)**: Makine öğrenme modellerinde ayarlanabilen değişkenlerdir. Eğitim sırasında bu parametreler optimize edilir.
- **Toplu İş Boyutu (Batch Size)**: Bir eğitim adımında modele verilen örnek sayısını ifade eder.
- **İletişim Yükü (Communication Overhead)**: Farklı işlem birimleri arasında veri iletiminden kaynaklanan gecikme ve maliyet.
- **Ardışık Düzen (Pipeline)**: Büyük görevleri daha küçük alt görevlere bölerek sırayla işleme yöntemidir.
- **Tensör Paralelliği (Tensor Parallelism)**: Büyük tensörleri daha küçük parçalara bölerek farklı işlem birimlerinde eş zamanlı olarak işleme yöntemidir.

Bu teknik terimlerin açıklamaları, metinde geçen kavramların daha iyi anlaşılmasına yardımcı olmak içindir.

---

