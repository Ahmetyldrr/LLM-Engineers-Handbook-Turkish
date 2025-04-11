#Veri Artırma (Data Augmentation)

Bu bağlamda, veri artırma (data augmentation), hem veri örneklerinin niceliğini hem de niteliğini artırma sürecini ifade eder. Veri oluşturmanın (data generation) aksine, bu aşamada önceden var olan talimat örneklerini (instruction samples) girdi olarak kullanırız. Talimat ve cevap çiftlerini (pairs of instructions and answers) yeniden örneklemek (upsample) mümkün olsa da, veri artırma çoğunlukla mevcut örneklerin kalitesini artırmak için kullanılır. Özellikle iki yönüyle odaklanır: çeşitlilik (diversity) ve karmaşıklık (complexity). Bu alandaki öncü bir yaklaşım, basit talimatları daha nitelikli olanlara dönüştürmek için Büyük Dil Modellerini (LLMs - Large Language Models) kullanan Evol-Instruct yöntemidir. Geliştirilen talimatlar daha sonra güçlü Büyük Dil Modelleri kullanılarak cevaplar oluşturmak için kullanılabilir. Bu yöntem iki ana strateji kullanır: derinlemesine (in-depth) ve genişlemesine (in-breadth) evrim.

Derinlemesine evrim, mevcut talimatların karmaşıklığını artırmaya odaklanır. Birkaç teknik içerir: 
- Kısıtlamalar (Constraints): Orijinal talimata ek gereksinimler veya sınırlamalar ekleyerek yerine getirmeyi daha zor hale getirmeyi içerir.
- Derinleştirme (Deepening): Yüzeysel sorular yerine, daha kapsamlı cevaplar gerektiren daha derin sorular bulmaya çalışır.
- Somutlaştırma (Concretizing): Genel kavramları daha spesifik olanlarla değiştirerek talimata ayrıntı ve kesinlik ekler.
- Akıl yürütme adımlarını artırma (Increasing reasoning steps): Talimatları, açıkça çok adımlı akıl yürütme talep edecek şekilde değiştirerek daha karmaşık problem çözmeyi teşvik eder.
- Girdi karmaşıklığını artırma (Complicating input): XML, JSON veya kod parçaları gibi talimata daha karmaşık veri biçimleri veya yapıları eklemeyi içerir.

Genişlemesine evrim ise talimat veri setinin çeşitliliğini genişletmeyi amaçlar. Mevcut talimatlardan esinlenerek tamamen yeni talimatlar üretir ve aynı alan içinde daha nadir veya uzun kuyruklu (long-tailed) örnekler oluşturmaya odaklanır.

Somut bir uygulama örneği olarak, derinlemesine evrim, AutoEvol makalesindeki aşağıdaki prompt ile otomatikleştirilebilir. Sadece geliştirmek istediğiniz talimatı girdi olarak sağlamanız gerekir ve GPT-4o gibi güçlü bir model, orijinal talimatın daha karmaşık bir versiyonunu döndürecektir.

`Talimatı, daha karmaşık bir versiyona yeniden yazan bir Talimat Yeniden Yazıcı`sınız. Lütfen verilen “#Talimat#”ı daha karmaşık bir versiyona yeniden yazmak için aşağıdaki adımları takip edin.

Adım 1: Lütfen “#Talimat#”ı dikkatlice okuyun ve bu talimatı daha karmaşık hale getirmek için (ChatGPT ve GPT4 gibi iyi bilinen yapay zeka asistanları için biraz daha zor hale getirmek için) tüm olası yöntemleri listeleyin. Talimatın dilini değiştirme yöntemleri sağlamayın!

Adım 2: #Yöntemler Listesi# temelinde #Talimat#ı daha karmaşık hale getirmek için kapsamlı bir plan oluşturun. Plan, #Yöntemler Listesi#nden birkaç yöntem içermelidir.

Adım 3: Planı adım adım uygulayın ve #Yeniden Yazılmış Talimat#ı sağlayın. #Yeniden Yazılmış Talimat#, “#Talimat#”a sadece 10 ila 20 kelime ekleyebilir.

Adım 4: Lütfen #Yeniden Yazılmış Talimat#ı dikkatlice gözden geçirin ve mantıksız kısımları belirleyin. #Yeniden Yazılmış Talimat#ın yalnızca #Talimat#ın daha karmaşık bir versiyonu olduğundan emin olun. Lütfen yalnızca #Son Haline Getirilmiş Talimat#ı herhangi bir açıklama yapmadan sağlayın.

Lütfen cevabı kesinlikle aşağıdaki biçimde verin:
Adım 1 #Yöntemler Listesi#:
Adım 2 #Plan#:
Adım 3 #Yeniden Yazılmış Talimat#:
Adım 4 #Son Haline Getirilmiş Talimat#:
#Talimat#: {Talimat}

Tablo 5.4 – Zeng ve arkadaşları (2024) tarafından “Büyük Dil Modelleri için Otomatik Talimat Evrimi” makalesinden Evol LLM promptu.

UltraFeedback yöntemi, talimat kalitesine değil, cevap kalitesine odaklanan bir başka yenilikçi yaklaşımdır. Model cevaplarının kalitesini ve çeşitliliğini artırmak için yapay zeka geri bildirimini (AI feedback) kullanır. Talimatları evrimleştiren Evol-Instruct'in aksine, UltraFeedback, geniş bir talimat ve model havuzu kullanarak geniş bir cevap aralığı oluşturur. Daha sonra GPT-4 gibi gelişmiş dil modellerini kullanarak bu cevaplar için talimat takibi, doğruluğu, dürüstlüğü ve yararlılık gibi birçok boyutta ayrıntılı eleştiriler ve sayısal puanlar sağlar.

Bu fikirlere dayanarak, daha zorlayıcı ve çeşitli bir talimat veri seti oluşturmak için kendi artırma tekniklerinizi oluşturabilirsiniz. Mevcut talimatları ve cevapları geliştirerek ve evrimleştirerek, ortaya çıkan veri seti, modelleri daha karmaşık, çok adımlı görevleri yerine getirmek üzere daha iyi eğitebilir ve daha geniş bir uygulama yelpazesinde performanslarını geliştirebilir.

### Kod Açıklaması

Verilen metinde, bir prompt örneği verilmiştir. Bu prompt, bir talimatı daha karmaşık hale getirmek için kullanılır. Promptun her bir adımını açıklayalım:

1. **Adım 1: Yöntemlerin Listelenmesi**
   - Bu adımda, verilen talimatı daha karmaşık hale getirmek için olası yöntemler listelenir. Örneğin, talimata kısıtlamalar eklemek, daha derin sorular sormak, genel kavramları somutlaştırmak gibi.

2. **Adım 2: Plan Oluşturma**
   - Liste oluşturulduktan sonra, bu listedeki yöntemleri kullanarak talimatı daha karmaşık hale getirmek için bir plan yapılır. Bu plan, birkaç yöntemi içerebilir.

3. **Adım 3: Talimatı Yeniden Yazma**
   - Oluşturulan plana göre, talimat yeniden yazılır. Bu yeniden yazılmış talimat, orijinal talimattan daha karmaşık olmalıdır ve sadece 10 ila 20 kelime eklenebilir.

4. **Adım 4: Son Kontroller**
   - Yeniden yazılmış talimat gözden geçirilir ve mantıksız kısımlar belirlenir. Talimatın gerçekten daha karmaşık bir versiyonu olduğundan emin olunur ve son haline getirilir.

Bu prompt, GPT-4o gibi güçlü dil modelleri ile kullanılmak üzere tasarlanmıştır. Kullanıcı, geliştirmek istediği talimatı girdi olarak sağlar ve model, daha karmaşık bir talimat versiyonu döndürür.

---

