# ZenML Veri Toplama İşlem Hattını Yönetir (Orchestrates the Data Collection Pipeline)

ZenML, veri toplama işlem hattını yönetir (orchestrates the data collection pipeline). Böylece, ZenML'i kullanarak, veri toplama işlem hattını manuel olarak çalıştırabilir, zamanlayabilir veya belirli olaylar tarafından tetikleyebilirsiniz. Burada, manuel olarak nasıl çalıştırılacağını gösteriyoruz, diğer senaryoları ise Bölüm 11'de MLOps'a daha derinlemesine daldığımızda tartışacağız.

Farklı bir işlem hattı çalıştırması (pipeline run) her yazar için yapılandırdık. Paul Iusztin'in veya Maxime Labonne'un verileri için bir ZenML yapılandırma dosyası sağladık. Örneğin, Maxime'in verilerini toplamak için veri toplama işlem hattını çağırmak üzere aşağıdaki CLI komutunu çalıştırabilirsiniz: 
```
poetry poe run-digital-data-etl-maxime
```
Bu, aşağıdaki ZenML YAML yapılandırma dosyasıyla işlem hattını çağırır:
```
parameters:
  user_full_name: Maxime Labonne # [First Name(s)] [Last Name]
  links:
    # Personal Blog
    - https://mlabonne.github.io/blog/posts/2024-07-29_Finetune_Llama31.html
    - https://mlabonne.github.io/blog/posts/2024-07-15_The_Rise_of_Agentic_Data_Generation.html
    # Substack
    - https://maximelabonne.substack.com/p/uncensor-any-llm-with-abliteration-d30148b7d43e
    - https://maximelabonne.substack.com/p/create-mixtures-of-experts-with-mergekit-11b318c99562
    - https://maximelabonne.substack.com/p/merge-large-language-models-with-mergekit-2118fb392b54
    … # More Substack links
```
Önceki Şekil 3.3'te, işlem hattının çalıştırma DAG'sini (run DAG) ve ZenML'in gösterge tablosundaki ayrıntıları gördük. Bu arada, Şekil 3.5, bu veri toplama işlem hattı tarafından oluşturulan kullanıcı çıktı eserini (user output artifact) gösterir. Bu özel çalıştırma için topladığımız bağlantıları içeren MongoDB veritabanından alınan kullanıcı sorgusunu ve alınan kullanıcıyı inceleyebilirsiniz.

# Şekil 3.5: Maxime'in Yapılandırma Dosyasını Kullanarak Veri Toplama İşlem Hattını Çalıştırdıktan Sonra Kullanıcı Çıktı Eseri Örneği

Ayrıca, Şekil 3.6'da, topladığımız verilerin alındığı tüm alanları listeleyen crawled_links çıktı eserini (output artifact), her alan için taranan toplam bağlantı sayısını ve başarıyla toplanan bağlantı sayısını gözlemleyebilirsiniz. Bu eserlerin gücünü bir kez daha vurgulamak isteriz, çünkü her işlem hattının sonuçlarını ve meta verilerini izlerler, böylece her işlem hattı çalıştırmasını bireysel olarak izlemek ve hata ayıklamak son derece kolaydır.

# Şekil 3.6: Maxime'in Yapılandırma Dosyasını Kullanarak Veri Toplama İşlem Hattını Çalıştırdıktan Sonra crawled_links Çıktı Eseri Örneği

Şimdi, aşağıdaki kodu çalıştırarak crawled_links eserini kodumuzun herhangi bir yerinde indirebiliriz; burada eserin kimliği (ID) ZenML'de bulunabilir ve her eser sürümü için benzersizdir:
```python
from zenml.client import Client
artifact = Client().get_artifact_version('8349ce09-0693-4e28-8fa2-20f82c76ddec')
loaded_artifact = artifact.load()
```
Örneğin, aynı veri toplama işlem hattını ancak Paul Iusztin'in YAML yapılandırmasıyla kolayca çalıştırabiliriz; aşağıda listelenmiştir:
```
parameters:
  user_full_name: Paul Iusztin # [First Name(s)] [Last Name]
  links:
    # Medium
    - https://medium.com/decodingml/an-end-to-end-framework-for-production-ready-llm-systems-by-building-your-llm-twin-2cc6bb01141f
    - https://medium.com/decodingml/a-real-time-retrieval-system-for-rag-on-social-media-data-9cc01d50a2a0
    - https://medium.com/decodingml/sota-python-streaming-pipelines-for-fine-tuning-llms-and-rag-in-real-time-82eb07795b87
    … # More Medium links
    # Substack
    - https://decodingml.substack.com/p/real-time-feature-pipelines-with?r=1ttoeh
    - https://decodingml.substack.com/p/building-ml-systems-the-right-way?r=1ttoeh
    - https://decodingml.substack.com/p/reduce-your-pytorchs-code-latency?r=1ttoeh
    … # More Substack links
```
İşlem hattını Paul'un yapılandırmasıyla çalıştırmak için aşağıdaki poe komutunu çağırırız:
```
poetry poe run-digital-data-etl-paul
```
Bu, altında yatan aşağıdaki CLI komutunu çağırır ve Paul'un yapılandırma dosyasını referans alır:
```
poetry run python -m tools.run --run-etl --no-cache --etl-config-filename digital_data_etl_paul_iusztin.yaml
```
Tüm yapılandırmaları repository'deki configs/ dizininde bulabilirsiniz. Ayrıca, poe kullanarak, desteklenen tüm yazarlar için veri toplama işlem hattını çağıran bir komut yapılandırdık:
```
poetry poe run-digital-data-etl
```
MongoDB veri ambarımızı (data warehouse) ODM sınıflarımızı kullanarak kolayca sorgulayabiliriz. Örneğin, Paul Iusztin için toplanan tüm makaleleri sorgulayalım:
```python
from llm_engineering.domain.documents import ArticleDocument, UserDocument
user = UserDocument.get_or_create(first_name="Paul", last_name="Iusztin")
articles = ArticleDocument.bulk_find(author_id=str(user.id))
print(f"User ID: {user.id}")
print(f"User name: {user.first_name} {user.last_name}")
print(f"Number of articles: {len(articles)}")
print("First article link:", articles[0].link)
```
Yukarıdaki kodun çıktısı:
```
User ID: 900fec95-d621-4315-84c6-52e5229e0b96
User name: Paul Iusztin
Number of articles: 50
First article link: https://medium.com/decodingml/an-end-to-end-framework-for-production-ready-llm-systems-by-building-your-llm-twin-2cc6bb01141f
```
Sadece iki satır kodla, projemiz içinde tanımlanan herhangi bir ODM kullanarak MongoDB veri ambarımızı sorgulayabilir ve filtreleyebiliriz. Ayrıca, veri toplama işlem hattınızın beklendiği gibi çalıştığından emin olmak için, ayrı olarak yüklemeniz gereken IDE'nizin MongoDB eklentisini kullanarak MongoDB koleksiyonlarınızı arayabilirsiniz. Örneğin, VSCode için bu eklentiyi kullanabilirsiniz: https://www.mongodb.com/products/tools/vs-code. Diğer IDE'ler için benzer eklentileri veya harici NoSQL görselleştirme araçlarını kullanabilirsiniz. MongoDB görselleştirme aracına bağlandıktan sonra, aşağıdaki URI'yi kullanarak yerel veritabanımıza bağlanabilirsiniz: `mongodb://llm_engineering:llm_engineering@127.0.0.1:27017`. Bulut MongoDB kümesi için URI'yi değiştirmelisiniz; bunu Bölüm 11'de inceleyeceğiz.

Ve böylece, farklı ZenML yapılandırmalarıyla veri toplama işlem hattını nasıl çalıştıracağınızı ve her çalıştırmanın çıktı eserlerini nasıl görselleştireceğinizi öğrendiniz. Ayrıca, belirli bir veri kategorisi ve yazar için veri ambarını nasıl sorgulayacağımızı da gördük. Böylece, veri mühendisliği bölümümüzü tamamladık ve sonuca geçebiliriz.

**Kod Açıklamaları**

1. `from zenml.client import Client`: ZenML Client sınıfını içe aktarır.
2. `artifact = Client().get_artifact_version('8349ce09-0693-4e28-8fa2-20f82c76ddec')`: Belirtilen ID'ye sahip eser sürümünü alır.
3. `loaded_artifact = artifact.load()`: Eseri yükler.
4. `from llm_engineering.domain.documents import ArticleDocument, UserDocument`: ArticleDocument ve UserDocument sınıflarını içe aktarır.
5. `user = UserDocument.get_or_create(first_name="Paul", last_name="Iusztin")`: Paul Iusztin kullanıcısını alır veya oluşturur.
6. `articles = ArticleDocument.bulk_find(author_id=str(user.id))`: Belirtilen yazar ID'sine sahip makaleleri toplar.
7. `print(f"User ID: {user.id}")`: Kullanıcı ID'sini yazdırır.
8. `print(f"User name: {user.first_name} {user.last_name}")`: Kullanıcı adını yazdırır.
9. `print(f"Number of articles: {len(articles)}")`: Makale sayısını yazdırır.
10. `print("First article link:", articles[0].link)`: İlk makalenin bağlantısını yazdırır.

**İngilizce Teknik Terimler**

* Orchestrates: Yönetir
* Pipeline: İşlem hattı
* Artifact: Eser
* DAG: Yönlendirilmiş Asiklik Çizge (Directed Acyclic Graph)
* ODM: Nesne-Doküman Eşleme (Object-Document Mapping)
* URI: Uniform Resource Identifier (Tekdüzen Kaynak Tanımlayıcısı)

---

