# MongoDB Kümesi Oluşturma ve Projeye Entegrasyonu

Aşağıdaki adımları takip ederek ücretsiz bir MongoDB kümesi (cluster) oluşturup projelerimize entegre edeceğiz.

## Adım 1: MongoDB Hesabı Oluşturma ve Küme (Cluster) Oluşturma

1. https://www.mongodb.com adresine gidin ve bir hesap oluşturun.
2. Sol panelde, Deployment | Database bölümüne gidin ve Build a Cluster butonuna tıklayın.
3. Oluşturma formunda aşağıdaki adımları takip edin:
   - M0 Free kümesi (cluster) seçin.
   - Kümenizin (cluster) adını twin olarak adlandırın.
   - AWS'i sağlayıcı (provider) olarak seçin.
   - Frankfurt (eu-central-1) bölgesini seçin. 
     - Farklı bir bölge seçebilirsiniz, ancak gelecekteki tüm AWS hizmetleri için aynı bölgeyi seçmeye dikkat edin.
   - Diğer özelliklerin varsayılan değerlerini bırakın.
   - Sağ alt köşede Create Deployment yeşil butonuna tıklayın.

## Adım 2: MongoDB Kümesine Bağlanma

1. Yeni oluşturulan MongoDB kümesinin (cluster) düzgün çalıştığını test etmek için yerel makinenizden ona bağlanmanız gerekir.
2. Biz MongoDB VS Code eklentisini (extension) kullandık, ancak başka bir araç da kullanabilirsiniz.
3. Choose a connection method kurulum akışından MongoDB for VS Code seçeneğini seçin.
4. Sitede verilen adımları takip edin.
5. Bağlanmak için, VS Code eklentisine (veya tercih ettiğiniz başka bir araca) DB bağlantı URL'sini (connection URL) yapıştırmanız gerekir. Bu URL, kullanıcı adınızı (username), parolanızı (password) ve küme URL'sini içerir. 
   - Örnek: `mongodb+srv://<kullanici_adiniz>:<parolaniz>@twin.vhxy1.mongodb.net`
   - Bu URL'yi daha sonra kopyalayabileceğiniz bir yere kaydedin.
6. Eğer parolanızı bilmiyor veya değiştirmek istiyorsanız, sol panelde Security → Quickstart bölümüne gidin. 
   - Orada giriş bilgilerinizi (login credentials) düzenleyebilirsiniz.
   - Bunları daha sonra erişebileceğiniz güvenli bir yere kaydettiğinizden emin olun.

## Adım 3: Güvenlik Ayarları

1. Bağlantılarınızın çalıştığını doğruladıktan sonra, sol panelde Security → Network Access bölümüne gidin ve ADD IP ADDRESS butonuna tıklayın.
2. ALLOW ACCESS FROM ANYWHERE seçeneğine tıklayın ve Confirm butonuna basın.
   - Basitlik açısından, herhangi bir IP'den herhangi bir makineye MongoDB kümesine erişime izin veriyoruz.
   - Bu, pipeline'larımızın ek karmaşık ağ kurulumu olmadan DB'ye sorgu yapmasını veya yazmasını sağlar.
   - Üretim için en güvenli seçenek olmasa da, örneğimiz için gayet iyi.

## Adım 4: Projeye Entegrasyon

1. Projenize geri dönün ve `.env` dosyasını açın.
2. `DATABASE_HOST` değişkenini MongoDB bağlantı dizesi (connection string) ile ekleyin veya değiştirin.
   - Örnek: `DATABASE_HOST=mongodb+srv://<kullanici_adiniz>:<parolaniz>@twin.vhxy1.mongodb.net`
3. Artık yerel MongoDB'niz yerine yeni oluşturduğumuz bulut MongoDB kümesini (cluster) kullanacaksınız.

Kod Açıklaması:

- `mongodb+srv://<kullanici_adiniz>:<parolaniz>@twin.vhxy1.mongodb.net` : Bu, MongoDB bağlantı URL'sidir (connection URL). 
  - `<kullanici_adiniz>`: Kullanıcı adınızı temsil eder.
  - `<parolaniz>`: Parolanızı temsil eder.
  - `twin.vhxy1.mongodb.net`: Kümenizin (cluster) URL'sidir.

Bu şekilde, MongoDB kümesi (cluster) oluşturma ve projeye entegrasyon işlemleri tamamlanır. Şimdi, benzer bir süreci Qdrant ile tekrarlayacağız.

İngilizce Teknik Terimlerin Türkçe Karşılıkları Parantez İçinde Eklenmiştir:
- cluster (küme)
- deployment (dağıtım)
- database (veritabanı)
- provider (sağlayıcı)
- region (bölge)
- attributes (özellikler)
- connection URL (bağlantı URL'si)
- username (kullanıcı adı)
- password (parola)
- login credentials (giriş bilgileri)
- IP address (IP adresi)
- connection string (bağlantı dizesi)

---

