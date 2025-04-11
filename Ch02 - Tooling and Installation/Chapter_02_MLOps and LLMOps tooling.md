# MLOps ve LLMOps Araçları

Bu bölüm, kitap boyunca kullanacağımız tüm MLOps ve LLMOps araçlarını ve bunların MLOps en iyi uygulamalarını kullanarak makine öğrenimi (ML) sistemleri oluşturmadaki rollerini hızlı bir şekilde tanıtacaktır. Kitabın bu noktasında, model kayıtları (model registries) ve orkestratörler (orchestrators) gibi LLM Twin kullanım senaryosunu uygulamak için kullanacağımız tüm MLOps bileşenlerini ayrıntılı olarak anlatmayı amaçlamıyoruz, ancak yalnızca ne oldukları ve nasıl kullanılacakları hakkında hızlı bir fikir vermeyi amaçlıyoruz. LLM Twin projesini kitap boyunca geliştirirken, tüm bu araçları nasıl kullandığımıza dair pratik örnekler göreceksiniz. Bölüm 11'de, MLOps ve LLMOps teorisine derinlemesine dalacağız ve tüm noktaları birbirine bağlayacağız. MLOps ve LLMOps alanları son derece pratik olduğundan, bu yönlerin teorisini sona bırakacağız, çünkü LLM Twin kullanım senaryosu uygulamasını inceledikten sonra anlaşılması çok daha kolay olacaktır. Ayrıca, bu bölüm her bir aracın nasıl kurulacağını göstermek için tasarlanmamıştır. Esas olarak her bir aracın ne için kullanıldığına ve bu kitap boyunca kullanılan temel özelliklere odaklanmaktadır. Yine de Docker kullanarak, tüm altyapıyı yerel olarak hızlı bir şekilde çalıştırabilirsiniz. Kitap içindeki adımları kendiniz uygulamak istiyorsanız, üç basit adımda uygulamayı yerel olarak barındırabilirsiniz: 
- Docker 27.1.1 (veya daha yüksek) sürümünün kurulu olması gerekir.
- `.env` dosyasını, repository README'de açıklanan tüm gerekli kimlik bilgileriyle doldurun.
- ZenML (http://127.0.0.1:8237/), MongoDB ve Qdrant veritabanlarını yerel olarak çalıştırmak için `poetry poe local-infrastructure-up` komutunu çalıştırın.

LLM-Engineers-Handbook repository README'de her şeyi yerel olarak nasıl çalıştıracağınıza dair daha fazla ayrıntı okuyabilirsiniz: https://github.com/PacktPublishing/LLM-Engineers-Handbook. Kitap içinde, her bir bileşeni buluta nasıl dağıtacağınızı da göstereceğiz.

Kodların ayrıntılı açıklaması:
1. `Have Docker 27.1.1 (or higher) installed.` 
   - Bu satır, Docker'ın 27.1.1 veya daha yüksek bir sürümünün kurulu olmasını gerektirmektedir. Docker, konteynerleştirme (containerization) için kullanılan bir platformdur.

2. `Fill your .env file with all the necessary credentials as explained in the repository README.`
   - Bu satır, `.env` dosyasını repository'deki README'de açıklanan tüm gerekli kimlik bilgileriyle doldurmanızı gerektirmektedir. `.env` dosyası, çevre değişkenlerini (environment variables) saklamak için kullanılır.

3. `Run poetry poe local-infrastructure-up to locally spin up ZenML (http://127.0.0.1:8237/) and the MongoDB and Qdrant databases.`
   - Bu satır, `poetry poe local-infrastructure-up` komutunu çalıştırarak ZenML, MongoDB ve Qdrant veritabanlarını yerel olarak başlatmanızı gerektirmektedir. 
   - `poetry`, Python projelerinde bağımlılıkları (dependencies) yönetmek için kullanılan bir araçtır.
   - `poe`, `poetry` ile birlikte kullanılan bir task runner'dır.
   - `local-infrastructure-up`, yerel altyapıyı başlatmak için kullanılan bir komuttur.
   - ZenML, makine öğrenimi (ML) işlemlerini yönetmek için kullanılan bir platformdur.
   - MongoDB ve Qdrant, veritabanı olarak kullanılan teknolojilerdir.

İngilizce teknik terimlerin Türkçeleri parantez içinde eklenmiştir:
- MLOps: Makine Öğrenimi Operasyonları
- LLMOps: Büyük Dil Modeli Operasyonları
- Model registries: Model Kayıtları
- Orchestrators: Orkestratörler
- Containerization: Konteynerleştirme
- Environment variables: Çevre Değişkenleri
- Dependencies: Bağımlılıklar
- Task runner: Görev Çalıştırıcı

---

