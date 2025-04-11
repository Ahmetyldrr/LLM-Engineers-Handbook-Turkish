#MLOps Nedir?

Tahmin etmiş olabileceğiniz gibi, MLOps DevOps ilkelerini Makine Öğrenimi (ML)'ne uygulamaya çalışır. Temel sorun, bir ML uygulamasının standart bir yazılım uygulamasına kıyasla veri, model ve son olarak kod gibi birçok başka hareketli parçaya sahip olmasıdır. MLOps, daha iyi yeniden üretilebilirlik (reproducibility), sağlamlık (robustness) ve kontrol için tüm bu kavramları izlemeyi, operasyon haline getirmeyi ve izlemeyi amaçlar. ML sistemlerinde, bir derleme (build) bu alanlardaki herhangi bir değişiklik tarafından tetiklenebilir - ister kodda bir güncelleme, ister verilerde değişiklikler veya modele yapılan ayarlamalar olsun. 
Şekil 11.2: Veri, model ve kod değişiklikleri arasındaki ilişki

DevOps'ta her şey kod etrafında döner. Örneğin, kod tabanına yeni bir özellik eklendiğinde, CI/CD hattını (pipeline) tetiklemeniz gerekir. MLOps'ta kod değişmezken sadece veri değişebilir. Bu durumda, yeni bir model eğitmek (veya ince ayar yapmak) gerekir, bu da yeni bir veri kümesi ve model sürümü ile sonuçlanır. Sezgisel olarak, bir bileşen değiştiğinde, diğerlerinden bir veya daha fazlasını etkiler. Böylece, MLOps tüm bu ekstra karmaşıklığı dikkate almak zorundadır. 
Verilerde ve dolaylı olarak modelde bir değişikliği tetikleyebilecek birkaç örnek:
- ML modelini dağıttıktan sonra, performansı zamanla bozulabilir, bu nedenle onu yeniden eğitmek için yeni verilere ihtiyacımız vardır.
- Gerçek dünyada veri toplama konusunda nasıl anlaşılırsa, sorunumuz için veri elde etmenin zor olduğunu fark edebiliriz, bu nedenle gerçek dünya kurulumumuzla çalışacak şekilde yeniden formüle etmemiz gerekir.
- Deneme aşamasında ve model eğitiminde, genellikle daha fazla veri toplamak veya yeniden etiketlemek zorundayız, bu da yeni bir model kümesi oluşturur.
- Modeli üretim ortamında sunduktan ve son kullanıcıların geri bildirimlerini topladıktan sonra, modeli eğitmek için yaptığımız varsayımların yanlış olduğunu fark edebiliriz, bu nedenle modelimizi değiştirmeliyiz.

Peki MLOps nedir? MLOps'un daha resmi bir tanımı aşağıdaki gibidir:
MLOps, DevOps alanının bir uzantısıdır ve DevOps metodolojisini koruyarak veri ve modelleri birinci sınıf vatandaş (first-class citizen) haline getirir. DevOps gibi, MLOps da ML model geliştirmenin dağıtım sürecinden (ML operasyonları) izole edilmesinin sistemin genel kalitesini, şeffaflığını ve çevikliğini azalttığı fikrinden kaynaklanır. Bunu akılda tutarak, optimal bir MLOps deneyimi, ML varlıklarını (assets) bir CI/CD ortamında diğer yazılım varlıkları gibi tutarlı bir şekilde, uyumlu bir sürüm (release) sürecinin bir parçası olarak ele alır.

**İngilizce Teknik Terimlerin Türkçeleri ve Açıklamaları Parantez İçinde**

- MLOps: Makine Öğrenimi Operasyonları
- DevOps: Geliştirme (Development) ve Operasyon (Operations) 
- ML: Makine Öğrenimi (Machine Learning)
- CI/CD: Sürekli Entegrasyon (Continuous Integration) / Sürekli Teslimat (Continuous Delivery) veya Sürekli Dağıtım (Continuous Deployment)
- Reproducibility: Yeniden üretilebilirlik
- Robustness: Sağlamlık
- Build: Derleme
- Pipeline: Hattı, Boru Hattı
- First-class citizen: Birinci sınıf vatandaş

**Kodların Ayrıntılı Açıklaması**

Paragraf içinde kod bulunmamaktadır. Ancak CI/CD pipeline gibi teknik terimlerle ilgili açıklamalar yapılabilir.

- `CI/CD pipeline`: CI/CD hattı, yazılım geliştirme sürecinde kod değişikliklerinin otomatik olarak test edilmesini, doğrulanmasını ve üretim ortamına dağıtılmasını sağlayan bir dizi işlemdir. 
  - `CI (Continuous Integration)`: Kod değişikliklerinin sık sık bir araya getirilmesini ve otomatik testlerle doğrulanmasını ifade eder.
  - `CD (Continuous Delivery/Deployment)`: Kod değişikliklerinin otomatik olarak üretim ortamına dağıtılmasını ifade eder. 
  - Pipeline, bu işlemleri otomatik olarak gerçekleştiren bir yapıdır. 

Örneğin, bir CI/CD pipeline aşağıdaki adımları içerebilir:
1. Kod değişikliklerinin alınması (`git pull`)
2. Kodun derlenmesi (`build`)
3. Otomatik testlerin çalıştırılması (`unit test`, `integration test`)
4. Kodun doğrulanması (`code review`)
5. Üretim ortamına dağıtılması (`deployment`)

Bu işlemler, CI/CD araçları (örneğin, Jenkins, GitLab CI/CD) kullanılarak otomatikleştirilebilir.

---

