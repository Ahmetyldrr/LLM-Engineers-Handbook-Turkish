# Bu Bölümde Kullanılacak Araçlar

Bu bölüm, kitap boyunca, özellikle LLM Twin projesini uygularken ve dağıtırken kullanılacak tüm temel araçları sunar. Kitabın bu noktasında, derinlemesine LLM, RAG, MLOps veya LLMOps kavramlarını sunmayı planlamıyoruz. Belirli bir aracın nasıl kurulacağı ve neden seçildiği konusunda kendimizi tekrar etmekten kaçınmak için teknik yığınımızı ve ön koşulları hızlıca gözden geçireceğiz. 3. Bölüm'den başlayarak, internetten veri kazıyan bir veri toplama ETL'sini (Extract, Transform, Load) uygulayarak LLM Twin kullanım durumumuzu keşfetmeye başlayacağız. Bölümün ilk kısmında, Python ekosistemindeki araçları tanıtacağız. Birden fazla Python sürümünü yönetmek, sanal bir ortam oluşturmak ve projemizin çalışması için gereken bağımlılıkları yüklemek için bu araçları kullanacağız. Bu araçları tanıtırken, LLM-Engineers-Handbook deposunu (repository) yerel makinenize nasıl kuracağınızı da göstereceğiz (kodları kendiniz denemek isterseniz): https://github.com/PacktPublishing/LLM-Engineers-Handbook. Ardından, daha genel araçlarla başlayarak, model kaydı (model registry) gibi MLOps ve LLMOps araçlarını ve daha sonra LLM değerlendirmesi (LLM evaluation) ve prompt izleme (prompt monitoring) araçları gibi daha LLM odaklı araçları keşfedeceğiz. Ayrıca, ML ve MLOps arasındaki boşluğu dolduran bir orkestrator (orchestrator) olan ZenML kullanarak birden fazla ML pipeline'ı olan bir projeyi nasıl yöneteceğimizi anlayacağız. Ayrıca, NoSQL ve vektör depolama (vector storage) için hangi veritabanlarını kullanacağımızı hızlıca keşfedeceğiz. Bu bileşenlerin tümünü yerel makinenizde Docker kullanarak nasıl çalıştıracağınızı göstereceğiz. Son olarak, AWS'yi hızlıca gözden geçirecek ve bir AWS kullanıcısı ve erişim anahtarları (access keys) nasıl oluşturacağınızı ve bulut kaynaklarınızı programatik olarak manipüle etmek için AWS CLI'yi nasıl kuracağınızı ve yapılandıracağınızı göstereceğiz. Ayrıca, SageMaker'ı ve açık kaynaklı LLMLerimizi eğitmek ve dağıtmak için neden kullandığımızı keşfedeceğiz. Bu araçlara aşina iseniz, bu bölümü güvenle atlayabilirsiniz. Ayrıca, projenin nasıl kurulacağını ve gerekli tüm bileşenlerin nasıl ayarlanacağını repository'nin README'sinde açıklıyoruz. Böylece, kodları okurken çalıştırmayı planlıyorsanız, daha özlü bir belge olarak da kullanabilirsiniz. Tüm bunları özetlemek gerekirse, bu bölümde aşağıdaki konuları keşfedeceğiz:
- Python ekosistemi ve proje kurulumu
- MLOps ve LLMOps araçları
- Yapılandırılmamış ve vektör verilerini depolamak için veritabanları
- AWS için hazırlık

Bu bölümün sonunda, kitap boyunca kullanacağımız tüm araçların farkında olacaksınız. Ayrıca, LLM-Engineers-Handbook deposunu nasıl kuracağınızı, diğer araçları nasıl ayarlayacağınızı ve kodları okurken çalıştırırsanız bunları nasıl kullanacağınızı öğreneceksiniz.

### Kodların Ayrıntılı Açıklaması

Bu bölümde kod verilmediğinden, kod açıklaması da bulunmamaktadır. Ancak, LLM-Engineers-Handbook deposunu kurmak ve diğer araçları ayarlamak için gerekli adımlar README dosyasında açıklanmıştır.

### Teknik Terimlerin İngilizce Karşılıkları Parantez İçinde Eklenmesi

Zaten yukarıdaki çeviride teknik terimlerin İngilizce karşılıkları parantez içinde eklenmiştir. Örneğin, "veri toplama ETL'sini (Extract, Transform, Load)" ve "model kaydı (model registry)" gibi. 

### Kodların Birebir Yazılması

Bu bölümde kod bulunmamaktadır. Ancak, LLM-Engineers-Handbook deposuna https://github.com/PacktPublishing/LLM-Engineers-Handbook adresinden ulaşabilirsiniz.

### Başlıkların #baslik Şeklinde Yazılması

Yukarıdaki çeviride başlık "# Bu Bölümde Kullanılacak Araçlar" şeklinde yazılmıştır.

---

