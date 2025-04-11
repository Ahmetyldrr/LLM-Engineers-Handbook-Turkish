# AWS Hesabı Oluşturma ve Yapılandırma

AWS kullanıcı arayüzünü (UI/UX) değiştirebileceği için, AWS hesabı oluşturma konusunda size en iyi şekilde talimat vermek için resmi eğitimlerine yönlendiriyoruz: https://docs.aws.amazon.com/accounts/latest/reference/manage-acct-creating.html . AWS hesabınızı başarıyla oluşturduktan sonra, AWS konsoluna http://console.aws.amazon.com adresinden erişebilirsiniz. Kök kullanıcı e-posta adresini kullanarak oturum açın (oturum açma düğmesinin altında bulunur), ardından hesabınızın e-posta adresini ve parolasını girin.

Daha sonra, AWS'ye programatik olarak erişmek için erişim anahtarları (access keys) oluşturmalıyız. Bunu yapmanın en iyi yolu, öncelikle bu AWS resmi eğitiminde anlatıldığı gibi yönetici erişimine sahip bir IAM kullanıcısı oluşturmaktır: https://docs.aws.amazon.com/streams/latest/dev/setting-up.html Üretim hesapları için, her bir kullanıcıya yalnızca rolünü yerine getirmek için gereken izinleri veren en az ayrıcalıklı bir politika (policy of least privilege) ile izinler vermek en iyi uygulamadır. Ancak, test hesabımızın kurulumunu basitleştirmek için, yukarıdaki eğitimde açıklanan ve Şekil 2.13'te gösterilen Yönetici Erişimi (AdministratorAccess) yönetilen politikasını kullanacağız.

Şekil 2.13: IAM kullanıcı izin politikaları örneği

Daha sonra, yeni oluşturduğunuz IAM kullanıcısı için bir erişim anahtarı oluşturmanız gerekir. Bunu yapmak için aşağıdaki eğitimi kullanın: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html . Erişim anahtarları aşağıdaki gibi görünecektir:
```makefile
aws_access_key_id = <your_access_key_id>
aws_secret_access_key = <your_secret_access_key>
```
Bunları güvende bir yerde saklayın, çünkü bunları oluşturduktan sonra onlara tekrar erişemezsiniz. Ayrıca, bunları kiminle paylaştığınıza dikkat edin, çünkü AWS hesabınıza erişmek ve çeşitli AWS kaynaklarını (resources) manipüle etmek için kullanılabilirler.

Son adım, AWS CLI'yi (Command Line Interface) kurmak ve yeni oluşturduğunuz erişim anahtarları ile yapılandırmaktır. AWS CLI'yi aşağıdaki bağlantıyı kullanarak kurabilirsiniz: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html . AWS CLI'yi kurduktan sonra, `aws configure` komutunu çalıştırarak yapılandırabilirsiniz. İşte AWS yapılandırma örneğimiz:
```ini
[default]
aws_access_key_id = *************
aws_secret_access_key = ************
region = eu-central-1
output = json
```
AWS CLI'yi yapılandırma hakkında daha fazla ayrıntı için aşağıdaki eğitimi inceleyin: https://docs.aws.amazon.com/cli/v1/userguide/cli-configure-files.html . Ayrıca, projenizi AWS kimlik bilgilerinizle (credentials) yapılandırmak için `.env` dosyasındaki aşağıdaki değişkenleri doldurmanız gerekir:
```makefile
AWS_REGION="eu-central-1" # AWS bölgenizle değiştirin. Varsayılan olarak "eu-central-1" kullanıyoruz.
AWS_ACCESS_KEY="<your_aws_access_key>"
AWS_SECRET_KEY="<your_aws_secret_key>"
```

# Bu Kitaptaki Uygulamalı Görevlerle İlgili Maliyetler Hakkında Önemli Not

Bu kitapta kullanılan tüm bulut hizmetleri (cloud services), AWS hariç, ücretsiz seçeneklerine (freemium option) uyar. Bu nedenle, kişisel bir AWS hesabı kullanırsanız, bu kitabı takip ederken AWS maliyetlerinden sorumlu olacaksınız. Bazı hizmetler AWS Ücretsiz Katmanı (AWS Free Tier) kullanımına girerken, diğerleri girmeyebilir. Bu nedenle, faturalandırma konsolunuzu düzenli olarak kontrol etmekten siz sorumlu olacaksınız. Maliyetlerin büyük çoğunluğu SageMaker'ı eğitim ve çıkarım (inference) için test ederken ortaya çıkacaktır. Testlerimize göre, AWS maliyetleri bu kitapta ve depoda sağlanan spesifikasyonları kullanarak $50 ile $100 arasında değişebilir. Maliyetlerinizi izlemek için AWS belgelerine bakın: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html .

**Kodların Ayrıntılı Açıklaması**

1. `aws_access_key_id = <your_access_key_id>` : Bu satır, AWS erişim anahtarınızı (access key ID) temsil eder. `<your_access_key_id>` kısmını gerçek erişim anahtarınızla değiştirmeniz gerekir.
2. `aws_secret_access_key = <your_secret_access_key>` : Bu satır, AWS gizli erişim anahtarınızı (secret access key) temsil eder. `<your_secret_access_key>` kısmını gerçek gizli erişim anahtarınızla değiştirmeniz gerekir.
3. `[default]` : Bu satır, AWS CLI yapılandırma dosyasındaki varsayılan profili temsil eder.
4. `region = eu-central-1` : Bu satır, AWS bölgesini (region) temsil eder. `eu-central-1` varsayılan değerdir, ancak bunu kendi bölgenizle değiştirebilirsiniz.
5. `output = json` : Bu satır, AWS CLI'nin çıktı formatını (output format) temsil eder. `json` varsayılan değerdir.
6. `AWS_REGION="eu-central-1"` : Bu satır, `.env` dosyasındaki AWS bölgesini temsil eder. `eu-central-1` varsayılan değerdir, ancak bunu kendi bölgenizle değiştirebilirsiniz.
7. `AWS_ACCESS_KEY="<your_aws_access_key>"` : Bu satır, `.env` dosyasındaki AWS erişim anahtarını temsil eder. `<your_aws_access_key>` kısmını gerçek erişim anahtarınızla değiştirmeniz gerekir.
8. `AWS_SECRET_KEY="<your_aws_secret_key>"` : Bu satır, `.env` dosyasındaki AWS gizli erişim anahtarını temsil eder. `<your_aws_secret_key>` kısmını gerçek gizli erişim anahtarınızla değiştirmeniz gerekir.

---

