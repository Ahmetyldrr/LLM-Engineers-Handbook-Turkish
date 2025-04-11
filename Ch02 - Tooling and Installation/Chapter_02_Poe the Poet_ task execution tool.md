#Poe the Poet
Poe the Poet, Poetry'nin üzerinde CLI (Command-Line Interface) komutlarını yönetmek ve çalıştırmak için kullanılan bir eklentidir (plugin). Python projesinde görevleri tanımlamayı ve çalıştırmayı kolaylaştırarak otomasyonu (automation) ve komut çalıştırma işlemlerini basitleştirir. Diğer popüler seçenekler Makefile, Invoke veya shell script'leridir, ancak Poe the Poet, proje görevlerini yönetmek için ayrı shell script'leri veya Makefile'lar yazma ihtiyacını ortadan kaldırır, böylece bağımlılıklar (dependencies) için Poetry'nin zaten kullandığı aynı yapılandırma dosyasını kullanarak görevleri yönetmek için zarif bir yol sağlar. Poe the Poet ile çalışırken, tüm komutlarınızı bir README dosyasında veya başka bir belgede belgelemek yerine, bunları doğrudan pyproject.toml dosyasına ekleyebilir ve bir takma ad (alias) ile komut satırında çalıştırabilirsiniz. Örneğin, Poe the Poet kullanarak, bir pyproject.toml dosyasında aşağıdaki görevleri tanımlayabiliriz: 
```toml
[tool.poe.tasks]
test = "pytest"
format = "black ."
start = "python main.py"
```
Daha sonra bu görevleri poe komutunu kullanarak çalıştırabilirsiniz:
```bash
poetry poe test
poetry poe format
poetry poe start
```
Poe the Poet'u Poetry eklentisi olarak aşağıdaki şekilde kurabilirsiniz: 
```bash
poetry self add 'poethepoet[poetry_plugin]'
```
Sonuç olarak, uygulamanızı çalıştırmak için tüm CLI komutlarınızın üzerine bir facade (façade) olarak bir araç kullanmak gerekir. Bu, uygulamanın karmaşıklığını önemli ölçüde basitleştirir ve kutudan çıkan belgeleme (out-of-the-box documentation) görevi görerek işbirliğini artırır. pyenv ve Poetry'nin kurulu olduğunu varsayarsak, depoyu klonlamak (clone), bağımlılıkları (dependencies) yüklemek ve Poe the Poet'u Poetry eklentisi olarak eklemek için çalıştırmanız gereken tüm komutlar şunlardır:
```bash
git clone https://github.com/PacktPublishing/LLM-Engineers-Handbook.git
cd LLM-Engineers-Handbook
poetry install --without aws
poetry self add 'poethepoet[poetry_plugin]'
```
Projeyi tam olarak çalışır hale getirmek için, hala birkaç adım izlenmesi gerekiyor, örneğin kimlik bilgilerinizi içeren bir .env dosyası oluşturmak ve OpenAI ve Hugging Face'den token almak gibi. Ancak bu kitap bir kurulum kılavuzu değildir, bu nedenle tüm bu ayrıntıları depoya README'ye taşıdık çünkü yalnızca depoyu çalıştırmayı planlıyorsanız yararlıdır: https://github.com/PacktPublishing/LLM-Engineers-Handbook . Artık Python projemizi kurduğumuza göre, kitapta kullanacağımız MLOps araçlarını tanıtacağız. Bu araçlara zaten aşina iseniz, aşağıdaki araç bölümünü güvenle atlayabilir ve Databases for storing unstructured and vector data bölümüne geçebilirsiniz.

###Kod Açıklamaları

####pyproject.toml Dosyası
```toml
[tool.poe.tasks]
test = "pytest"  # pytest komutunu çalıştırır
format = "black ."  # black kod formatlayıcısını çalıştırır
start = "python main.py"  # python main.py komutunu çalıştırır
```
Bu kod, Poe the Poet'un pyproject.toml dosyasında görevleri tanımlamak için kullanılan yapılandırmayı gösterir.

####Poe Komutları
```bash
poetry poe test  # test görevini çalıştırır
poetry poe format  # format görevini çalıştırır
poetry poe start  # start görevini çalıştırır
```
Bu kod, tanımlanan görevleri poe komutunu kullanarak çalıştırmayı gösterir.

####Poe the Poet Kurulumu
```bash
poetry self add 'poethepoet[poetry_plugin]'  # Poe the Poet'u Poetry eklentisi olarak kurar
```
Bu kod, Poe the Poet'u Poetry eklentisi olarak kurmayı gösterir.

####Depo Klonlama ve Bağımlılıkları Yükleme
```bash
git clone https://github.com/PacktPublishing/LLM-Engineers-Handbook.git  # depoyu klonlar
cd LLM-Engineers-Handbook  # klonlanan depoya geçer
poetry install --without aws  # bağımlılıkları yükler (aws hariç)
poetry self add 'poethepoet[poetry_plugin]'  # Poe the Poet'u Poetry eklentisi olarak kurar
```
Bu kod, depoyu klonlamayı, bağımlılıkları yüklemeyi ve Poe the Poet'u Poetry eklentisi olarak kurmayı gösterir.

İngilizce teknik terimler parantez içinde eklenmiştir:
- CLI (Command-Line Interface)
- plugin (eklenti)
- automation (otomasyon)
- dependencies (bağımlılıklar)
- alias (takma ad)
- façade (façade)
- out-of-the-box documentation (kutu-dan çıkan belgeleme)
- clone (klonlamak)

---

