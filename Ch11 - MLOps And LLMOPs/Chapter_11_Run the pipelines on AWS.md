# Makine Öğrenimi (ML) Pipeline'larını AWS Üzerinde Çalıştırma

Makine öğrenimi (ML) pipeline'larını AWS üzerinde çalıştırmaya çok yakınız, ancak birkaç son adımı tamamlamamız gerekiyor. Varsayılan ZenML yığınıdan (stack) bu bölümde oluşturduğumuz AWS yığınına geçelim. Projenizin kök dizininden aşağıdaki komutu CLI'de çalıştırın: 
```bash
zenml stack set aws-stack
```
AWS ECR ZenML deposuna (repository) dönün ve resim URI'sini (image URI) kopyalayın (Şekil 11.9'da gösterildiği gibi). Ardından, `configs` dizinine gidin, `configs/end_to_end_data.yaml` dosyasını açın ve `settings.docker.parent_image` özelliğini ECR URL'niz ile güncelleyin, aşağıdaki gibi:
```yml
settings:
  docker:
    parent_image: <YOUR ECR URL>  #örneğin, 992382797823.dkr.ecr.eu-central-1.amazonaws.com/zenml-rlwlcs:latest
    skip_build: True
```
Pipeline'ı her zaman ECR'de bulunan en son Docker imajını (image) kullanacak şekilde yapılandırdık. Bu, yeni bir imaj gönderdiğimizde pipeline'ın otomatik olarak kodda yapılan son değişiklikleri alacağı anlamına gelir.

`.env` dosyamızdan tüm kimlik bilgilerini (credentials) ZenML gizli (secrets) özelliğine aktarıyoruz, bu özellik kimlik bilgilerinizi güvenli bir şekilde saklar ve pipeline'larınızda erişilebilir kılar:
```bash
poetry poe export-settings-to-zenml
```
Son adım, pipeline'ları zaman uyumsuz (asynchronous) olarak çalıştırmak için ayarlamaktır, böylece bitmelerini beklemek zorunda kalmayız, bu da zaman aşımı (timeout) hatalarına neden olabilir:
```bash
zenml orchestrator update aws-stack --synchronous=False
```
Artık ZenML, AWS yığını kullanacağını, özel Docker imajımızı ve kimlik bilgilerimize erişebileceğini biliyor, böylece kurulumu tamamladık. `end-to-end-data-pipeline`'ı aşağıdaki komutla çalıştırın:
```bash
poetry poe run-end-to-end-data-pipeline
```
Şimdi ZenML Cloud → Pipelines → `end_to_end_data`'ya gidin ve son çalıştırma işlemini açın. ZenML kontrol panelinde (dashboard), pipeline'ın son durumunu görselleştirebilirsiniz (Şekil 11.10'da gösterildiği gibi). Bu pipeline, veri ile ilgili tüm pipeline'ları tek bir çalıştırma işleminde çalıştırır.

# Kodların Ayrıntılı Açıklaması

1. `zenml stack set aws-stack` 
   - Bu komut, ZenML'de varsayılan yığından (stack) `aws-stack` adlı yığına geçiş yapar.

2. `settings.docker.parent_image: <YOUR ECR URL>`
   - Bu, `end_to_end_data.yaml` dosyasında yapılan bir güncellemedir. Burada `<YOUR ECR URL>` kısmı, AWS ECR'deki Docker imajınızın URL'si ile değiştirilmelidir.

3. `poetry poe export-settings-to-zenml`
   - Bu komut, `.env` dosyasındaki kimlik bilgilerini ZenML gizli (secrets) özelliğine aktarır.

4. `zenml orchestrator update aws-stack --synchronous=False`
   - Bu komut, `aws-stack` yığını için zaman uyumsuz (asynchronous) çalıştırma ayarını yapar.

5. `poetry poe run-end-to-end-data-pipeline`
   - Bu komut, `end-to-end-data-pipeline`'ı çalıştırır.

6. `poetry poe set-local-stack`
   - Bu komut, ZenML yığını olarak yerel (local) yığını ayarlar.

7. `zenml disconnect`
   - Bu komut, ZenML cloud kontrol panelinden bağlantıyı keser ve yerel versiyona geri dönmenizi sağlar.

# İngilizce Teknik Terimlerin Türkçeleri ve Açıklamaları

- **Stack**: Yığın, bir dizi hizmet veya araç kümesi.
- **Repository**: Depo, projelerin veya kodların saklandığı yer.
- **Image URI**: İmaja ait URI, Docker imajını tanımlayan benzersiz bir adres.
- **Docker Image**: Docker imajı, bir uygulamanın çalıştırılması için gerekli olan her şeyi içeren hafif ve taşınabilir bir paket.
- **Credentials**: Kimlik bilgileri, bir sisteme erişim için gereken bilgiler.
- **Secrets**: Gizli veriler, güvenlik nedeniyle korunması gereken veriler.
- **Orchestrator**: Zamanlayıcı veya orkestrasyon aracı, iş akışlarını yöneten sistem.
- **Synchronous**: Zaman uyumlu, işlemlerin sırayla ve birbirini bekleyerek yapılması.
- **Asynchronous**: Zaman uyumsuz, işlemlerin birbirini beklemeden, paralel olarak yapılması.
- **Pipeline**: İşlem hattı, bir dizi işlemin otomatik olarak çalıştırılması.
- **Config**: Yapılandırma, bir sistem veya uygulamanın ayarları.
- **CLI**: Komut satırı arayüzü, kullanıcıların komutları girmesini sağlayan metin tabanlı arayüz.

---

