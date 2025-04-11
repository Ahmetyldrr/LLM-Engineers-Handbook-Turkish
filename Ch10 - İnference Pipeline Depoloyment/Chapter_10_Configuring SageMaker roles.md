# AWS Kimlik ve Erişim Yönetimi (IAM) Kullanıcıları ve Rolleri Oluşturma

İlk adım, SageMaker altyapısını erişmek ve dağıtmak için uygun AWS Identity and Access Management (IAM) kullanıcılarını ve rollerini oluşturmaktır. AWS IAM, kimlerin kimlik doğrulayabileceğini ve herhangi bir aktörün neye erişebileceğini kontrol eder. IAM aracılığıyla yeni kullanıcılar (kişilere atanan) ve yeni roller (altyapınızdaki EC2 VM'ler gibi diğer aktörlere atanan) oluşturabilirsiniz. Tüm dağıtım süreci otomatiktir. Birkaç CLI komutu çalıştıracağız, ancak önce `.env` dosyasında AWS_ACCESS_KEY, AWS_SECRET_KEY ve AWS_REGION ortam değişkenlerinin doğru şekilde yapılandırıldığından emin olun. Bu adımda en kolay yol, yönetici rolüne bağlı kimlik bilgilerini kullanmaktır, çünkü sonraki adımlarda bu bölümün geri kalanında kullanılan daha dar IAM rollerini oluşturacağız. `.env` dosyanızı yapılandırdıktan sonra, şunları yapmalıyız:

SageMaker'ın kendisi, Elastic Container Registry (ECR) ve S3 gibi dağıtım için ihtiyaç duyduğumuz kaynakları oluşturmak ve silmekle sınırlı bir IAM kullanıcısı oluşturun. Bunu yapmak için aşağıdaki komutu çalıştırın:
```
poetry poe create-sagemaker-role
```
Bu komut, yeni bir AWS erişim ve gizli anahtarı içeren `sagemaker_user_credentials.json` adlı bir JSON dosyası oluşturacaktır. Bundan sonra, SageMaker ile ilgili her şeyi dağıtmak için bu kimlik bilgilerini kullanacağız, böylece yalnızca SageMaker ile ilişkili kaynakları değiştireceğiz. Aksi takdirde, yönetici hesabını kullanarak diğer AWS kaynaklarını yanlışlıkla değiştirebilir ve bu da ek maliyetlere veya diğer mevcut projelerin değiştirilmesine neden olabilir. Bu nedenle, kullanım durumunuza özel dar bir role sahip olmak iyi bir uygulamadır.

Son adım, JSON dosyasından yeni kimlik bilgilerini almak ve `.env` dosyasındaki AWS_ACCESS_KEY ve AWS_SECRET_KEY değişkenlerini güncellemek. Uygulamayı https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/infrastructure/aws/roles/create_sagemaker_role.py adresinde inceleyebilirsiniz.

Bir IAM yürütme rolü oluşturun. Bu rolü SageMaker dağıtımına bağlayacağız, böylece bizim adımıza diğer AWS kaynaklarına erişebilecek. Bu, bulut dağıtımları için standart bir uygulamadır, çünkü kimlik bilgilerinizdeki her makineyi doğrulamak yerine, altyapınızdan yalnızca gerekli olanı erişmelerine izin veren bir rol atarsınız. Bizim durumumuzda, SageMaker'a AWS S3, CloudWatch ve ECR'ye erişim sağlayacağız. Rolü oluşturmak için aşağıdaki komutu çalıştırın:
```
poetry poe create-sagemaker-execution-role
```
Bu komut, yeni oluşturulan rolün Amazon Resource Name (ARN) içeren `sagemaker_execution_role.json` adlı bir JSON dosyası oluşturacaktır. ARN, bulut altyapınızda herhangi bir AWS kaynağını tanımlamak için eklenen bir kimliktir. JSON dosyasından ARN değerini alın ve `.env` dosyasındaki AWS_ARN_ROLE değişkenini güncelleyin. Uygulamayı https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/infrastructure/aws/roles/create_execution_role.py adresinde inceleyebilirsiniz.

Sorun yaşarsanız, AWS CLI'yi `.env` dosyasındakiyle aynı AWS kimlik bilgileriyle yapılandırın ve işlemi tekrarlayın. AWS CLI'yi yüklemek için resmi belgeler: https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html.

`.env` dosyasındaki IAM kullanıcısını ve rolünü ayarlayarak, bunları ayarlarda Python nesnesinde otomatik olarak yükleyeceğiz ve sonraki adımlarda kullanacağız. Şimdi, gerçek dağıtıma geçelim.

**Kod Açıklaması**

1. `poetry poe create-sagemaker-role` komutu:
 * Bu komut, SageMaker için sınırlı bir IAM kullanıcısı oluşturur.
 * `sagemaker_user_credentials.json` adlı bir JSON dosyası oluşturur ve yeni AWS erişim ve gizli anahtarını içerir.
2. `poetry poe create-sagemaker-execution-role` komutu:
 * Bu komut, SageMaker için bir IAM yürütme rolü oluşturur.
 * `sagemaker_execution_role.json` adlı bir JSON dosyası oluşturur ve yeni oluşturulan rolün ARN'sini içerir.
3. `.env` dosyasındaki değişkenlerin güncellenmesi:
 * `AWS_ACCESS_KEY` ve `AWS_SECRET_KEY` değişkenleri, `sagemaker_user_credentials.json` dosyasından alınan yeni kimlik bilgileriyle güncellenir.
 * `AWS_ARN_ROLE` değişkeni, `sagemaker_execution_role.json` dosyasından alınan ARN değeriyle güncellenir.

Bu komutlar ve `.env` dosyasındaki değişkenlerin güncellenmesi, SageMaker altyapısını dağıtmak için gerekli IAM kullanıcılarını ve rollerini oluşturur.

---

