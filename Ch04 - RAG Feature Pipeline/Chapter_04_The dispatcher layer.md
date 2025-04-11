# Dispatcher (Dispatcher) ve Factory (Factory) Sınıfları ile Doküman İşleme

Bir dispatcher (dispatcher), bir dokümanı girdi olarak alır ve veri kategorisine (makale, gönderi veya depo) göre özel handler (handler) uygular. Bir handler, bir dokümanı temizleyebilir (clean), parçalayabilir (chunk) veya gömebilir (embed). `CleaningDispatcher` sınıfına yakından bakalım. Bu sınıf, ham bir dokümanı girdi olarak alan ve veri kategorisine göre temizleme mantığını uygulayan bir handler çağıran `dispatch()` metotunu uygular:

```python
class CleaningDispatcher:
    cleaning_factory = CleaningHandlerFactory()

    @classmethod
    def dispatch(cls, data_model: NoSQLBaseDocument) -> VectorBaseDocument:
        data_category = DataCategory(data_model.get_collection_name())
        handler = cls.cleaning_factory.create_handler(data_category)
        clean_model = handler.clean(data_model)
        logger.info("Data cleaned successfully.",
                    data_category=data_category,
                    cleaned_content_len=len(clean_model.content),
                    )
        return clean_model
```

**Kod Açıklaması:**

*   `CleaningDispatcher` sınıfı, `dispatch()` metotunu içerir.
*   `dispatch()` metotunu, `data_model` adlı bir `NoSQLBaseDocument` nesnesini girdi olarak alır ve `VectorBaseDocument` türünde bir nesne döndürür.
*   `data_category`, `data_model` nesnesinin koleksiyon adından (`get_collection_name()`) elde edilir.
*   `handler`, `CleaningHandlerFactory` sınıfının `create_handler()` metotunu çağırarak oluşturulur. Bu handler, `data_category` temelinde belirlenir.
*   `clean_model`, `handler` nesnesinin `clean()` metotunu çağırarak elde edilir. Bu, `data_model` nesnesini temizler.
*   `logger.info()` çağrısı, temizleme işleminin başarılı olduğunu loglar. Log mesajı, veri kategorisini (`data_category`) ve temizlenen içeriğin uzunluğunu (`cleaned_content_len`) içerir.

Dispatcher mantığının anahtarı, `CleaningHandlerFactory()` sınıfıdır. Bu sınıf, dokümanın veri kategorisine göre farklı temizleme handler'ları oluşturur:

```python
class CleaningHandlerFactory:
    @staticmethod
    def create_handler(data_category: DataCategory) -> CleaningDataHandler:
        if data_category == DataCategory.POSTS:
            return PostCleaningHandler()
        elif data_category == DataCategory.ARTICLES:
            return ArticleCleaningHandler()
        elif data_category == DataCategory.REPOSITORIES:
            return RepositoryCleaningHandler()
        else:
            raise ValueError("Unsupported data type")
```

**Kod Açıklaması:**

*   `CleaningHandlerFactory` sınıfı, `create_handler()` metotunu içerir.
*   `create_handler()` metotunu, `data_category` adlı bir `DataCategory` nesnesini girdi olarak alır ve `CleaningDataHandler` türünde bir nesne döndürür.
*   `data_category` temelinde, ilgili temizleme handler'ı oluşturulur. Örneğin, `DataCategory.POSTS` ise, `PostCleaningHandler()` nesnesi oluşturulur.
*   Desteklenmeyen bir veri kategorisi için `ValueError` hatası fırlatılır.

Dispatcher veya Factory sınıfları karmaşık değildir, ancak dokümanlarınıza çeşitli işlemler uygulamak için sezgisel ve basit bir arayüz sunarlar. Dokümanları işlerken, veri kategorisi hakkında endişelenmek yerine, bu işi halleden bir sınıfınız olur. Bu, yazılım mühendisliğinde DRY (Don't Repeat Yourself) prensiplerine saygı gösterir. DRY prensiplerine uyarak, tek bir hata noktası ve kolayca genişletilebilir kod elde edersiniz.

`ChunkingDispatcher` ve `EmbeddingDispatcher` sınıfları da aynı deseni takip eder. Bu sınıflar, sırasıyla `ChunkingHandlerFactory` ve `EmbeddingHandlerFactory` kullanır ve girdi dokümanın veri kategorisine göre doğru handler'ı oluşturur.

Tüm dispatcher ve factory sınıflarının kaynak kodu GitHub'da bulunabilir: https://github.com/PacktPublishing/LLM-Engineers-Handbook/blob/main/llm_engineering/application/preprocessing/dispatchers.py

Factory sınıfı, aynı arayüzü uygulayan bir sınıf ailesini oluşturan soyut fabrika tasarım desenini (abstract factory creational pattern) kullanır. Bizim durumumuzda, bu handler'lar, handler türüne bakılmaksızın `clean()` metotunu uygular.

Handler sınıfı ailesi de strateji davranış desenini (strategy behavioral pattern) kullanır. Bu desen, bir nesne içinde bir algoritmanın farklı varyantlarını kullanmak ve çalışma zamanında bir algoritmadan diğerine geçmek istediğinizde kullanılır.

Sezgisel olarak, dispatcher katmanımızda fabrika ve strateji desenlerinin kombinasyonu şu şekilde çalışır: Başlangıçta, verileri temizlemek istiyorduk, ancak veri kategorisini yalnızca çalışma zamanında biliyorduk, bu nedenle hangi stratejiyi uygulayacağımıza karar veremedik. Temizleme kodu etrafında tüm kodu yazabilir ve bir Handler() arayüzü altında mantığı soyutlayabiliriz. Bir veri noktası aldığımızda, soyut fabrika desenini uygular ve veri türü için doğru temizleme handler'ını oluştururuz. Sonunda, dispatcher katmanı handler'ı kullanır ve doğru stratejiyi yürütür.

Bunu yaparak:

*   Belirli bir veri kategorisi için mantığı izole ederiz.
*   Polymorphism'i kullanarak kodun yüzlerce if-else ifadesi içermesini önleriz.
*   Kodun modüler ve genişletilebilir olmasını sağlarız.

Yeni bir veri kategorisi geldiğinde, yalnızca Factory sınıfını değiştirmek ve yeni bir handler uygulamak yeterlidir, kodun diğer bölümlerine dokunmadan.

Şimdiye kadar, varlıklarımızı ve verilerin uygulamamızda nasıl aktığını modelledik. Henüz tek bir temizleme, parçalama veya gömme kodu yazmadık. Bu, hızlı bir demo ile üretim hazır bir uygulama arasındaki büyük bir farktır. Bir demoda, yazılım mühendisliği en iyi uygulamaları ve kodunuzu gelecekte kanıtlamak için yapılandırma konusunda endişelenmezsiniz. Ancak, gerçek dünya uygulaması oluştururken temiz, modüler ve ölçeklenebilir kod yazmak kritik önem taşır.

RAG özellik ardışık düzeninin son bileşeni, temizleme, parçalama ve gömme handler'larının uygulanmasıdır.

---

