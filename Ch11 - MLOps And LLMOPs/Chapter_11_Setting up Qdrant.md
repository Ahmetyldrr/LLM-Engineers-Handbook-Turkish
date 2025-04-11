# Qdrant Kümesi (Qdrant Cluster) Oluşturma ve Projemize Bağlama

MongoDB için yaptığımıza benzer bir süreci tekrar etmemiz gerekiyor. Bu nedenle, bir Qdrant kümesi oluşturmak ve projemize bağlamak için aşağıdaki adımları takip edin:

1. https://cloud.qdrant.io/ adresinden Qdrant'a gidin ve bir hesap oluşturun.
2. Sol panelde, Clusters'a gidin ve Create (Oluştur) butonuna tıklayın.
3. Küme oluşturma formunu aşağıdaki bilgilerle doldurun:
   - Kümenin Ücretsiz (Free) sürümünü seçin.
   - GCP'yi bulut sağlayıcı (cloud provider) olarak seçin (kitabı yazarken, ücretsiz küme için izin verilen tek seçenekti).
   - Bölge (region) olarak Frankfurt'u seçin (veya MongoDB için seçtiğiniz bölgeyi).
   - Kümenin adını twin olarak girin.
   - Diğer özelliklerin varsayılan değerlerini bırakın ve Create (Oluştur) butonuna tıklayın.

   ```bash
   # Adım 3'te yapılan işlemler
   # Küme oluşturma formunu doldurma
   Cluster Name: twin
   Cloud Provider: GCP
   Region: Frankfurt
   ```

4. Sol paneldeki Data Access Control bölümüne giderek küme erişim tokenı oluşturun. Create (Oluştur) butonuna tıklayın ve yeni erişim tokenı oluşturmak için twin kümesini seçin.
5. Yeni oluşturulan tokenı güvenli bir yere kopyalayın, çünkü daha sonra erişemezsiniz.

   ```python
   # Erişim tokenı oluşturma
   access_token = "olusturulan_token"
   ```

6. Bağlantınızın düzgün çalıştığını test etmek için Usage Examples'dan örnek kodu çalıştırabilirsiniz.
7. Qdrant'ın Clusters bölümüne geri dönün ve yeni oluşturduğunuz twin kümesini açın. Kümenin endpoint'ine (uç nokta) erişebileceksiniz, bu kodu Qdrant'ı yapılandırmak için ihtiyacınız olacak.

   ```bash
   # Qdrant kümesinin endpoint'i
   endpoint_url = "https://twin.qdrant.io"
   ```

8. Open Dashboard'a tıklayarak ve API Key'inizi parola olarak girerek Qdrant koleksiyonlarını ve dokümanlarını görselleştirebilirsiniz. Qdrant kümesi kontrol paneli şu anda boş olacak, ancak işlem hatlarını (pipelines) çalıştırdıktan sonra tüm koleksiyonları göreceksiniz.

   ```markdown
   # Qdrant Kümesi Kontrol Paneli
   ![Qdrant cluster dashboard example](figure_11_6.png)
   Figure 11.6: İki koleksiyonla doldurulmuş Qdrant kümesi kontrol paneli örneği.
   ```

9. Projemize geri dönün ve `.env` dosyasını açın. Şimdi, aşağıdaki gibi birkaç ortam değişkenini doldurmamız gerekiyor:

   ```makefile
   # .env dosyası
   USE_QDRANT_CLOUD = true
   QDRANT_CLOUD_URL = <7. adımdaki endpoint URL>
   QDRANT_APIKEY = <5. adımdaki erişim tokenı>
   ```

   ```python
   # .env dosyasını okuma
   import os
   use_qdrant_cloud = os.getenv("USE_QDRANT_CLOUD")
   qdrant_cloud_url = os.getenv("QDRANT_CLOUD_URL")
   qdrant_apikey = os.getenv("QDRANT_APIKEY")
   ```

10. İşte bu kadar! Yerel Qdrant vektör veritabanı yerine, yeni oluşturduğumuz bulut Qdrant kümesi ile okuyup yazacağız. Her şeyin düzgün çalıştığından emin olmak için, MongoDB ve Qdrant'ın bulut sürümleri ile uçtan uca veri işlem hattını (end-to-end data pipeline) aşağıdaki gibi çalıştırın:

    ```bash
    # Uçtan uca veri işlem hattını çalıştırma
    poetry run end-to-end-data-pipeline
    ```

11. Son adım, ZenML bulutunu kurmak ve tüm altyapımızı AWS'ye deploy etmektir.

Kodların ayrıntılı açıklamaları:

*   `.env` dosyasını düzenleyerek Qdrant kümesi için gerekli ortam değişkenlerini tanımlıyoruz.
*   `USE_QDRANT_CLOUD` değişkeni, Qdrant'ın bulut sürümünü kullanıp kullanmayacağımızı belirler.
*   `QDRANT_CLOUD_URL` değişkeni, Qdrant kümesinin endpoint URL'sini tanımlar.
*   `QDRANT_APIKEY` değişkeni, Qdrant kümesine erişim tokenını tanımlar.
*   `poetry run end-to-end-data-pipeline` komutu, uçtan uca veri işlem hattını çalıştırır ve verileri Qdrant kümesine yazar.

İngilizce teknik terimlerin Türkçe karşılıkları parantez içinde verildi:

*   Qdrant Cluster (Qdrant Kümesi)
*   Cloud Provider (Bulut Sağlayıcı)
*   Region (Bölge)
*   Endpoint (Uç Nokta)
*   API Key (API Anahtarı)
*   Access Token (Erişim Tokenı)
*   End-to-end Data Pipeline (Uçtan Uca Veri İşlem Hattı)

---

