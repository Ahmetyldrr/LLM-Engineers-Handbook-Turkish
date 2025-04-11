#Özellik Depolama ve İşleme Hattı (Feature Store and Pipeline)

Özellik deposu (Feature Store), eğitim ve çıkarım (inference) işlem hatlarında kullanılan tüm özellikler için merkezi erişim noktası olacaktır. Eğitim işlem hattı (Training Pipeline), özellik deposundan (özellikler artifact olarak depolanır) temizlenmiş verileri kullanarak LLMs'i (Large Language Models) fine-tune edecektir. Çıkarım işlem hattı (Inference Pipeline), RAG (Retrieval-Augmented Generation) için vektör veritabanına (Vector DB) chunklanmış belgeler için sorgulama yapacaktır. Bu nedenle, sadece bir RAG ingestion işlem hattı değil, bir özellik işlem hattı (Feature Pipeline) tasarlıyoruz. Pratikte, özellik işlem hattı birden fazla alt bileşen içerir ve bunlardan biri RAG mantığıdır (RAG logic). Özellik işlem hattının (Feature Pipeline) esas olarak makine öğrenimi (Machine Learning) sistemlerinin karmaşıklığını aşmak için bir zihin haritası olarak kullanıldığını unutmayın. Girdi olarak ham verileri (raw data) aldığını ve daha sonra özellik deposunda (Feature Store) depolanan özellikler ve isteğe bağlı etiketler (labels) ürettiğini açıkça belirtir. Bu nedenle, iyi bir sezgi, veri ambarı (Data Warehouse) ile özellik deposu arasındaki tüm mantığın, bir veya daha fazla alt işlem hattından oluşan özellik işlem hattı namespace'ine girdiğini düşünmektir. Örneğin, temizlenmiş verileri alan, bunları instruct veri kümelerine (instruct datasets) işleyen ve artifact olarak depolayan başka bir işlem hattı uygulayacağız; bu da mantıksal özellik deposunun (logical feature store) bir parçası olarak özellik işlem hattı şemsiyesi altına girer. Başka bir örnek, ham veriler veya hesaplanmış özellikler üzerinde bir veri doğrulama (data validation) işlem hattı uygulamak olabilir. 
Dikkate alınması gereken bir diğer önemli nokta, standart konvansiyonları takip ettiğinizde dizeler (strings) olarak depolanan metin verilerinin özellik olarak kabul edilmemesidir. Bir özellik, doğrudan modele beslenen (fed directly into the model) bir şeydir. Örneğin, instruct veri kümelerini veya chunklanmış belgeleri özellik olarak kabul edilebilmesi için tokenize etmemiz (tokenize) gerekir. Neden? Çünkü tokenler doğrudan modele beslenir, cümleler olarak değil. Ne yazık ki, bu sistemi daha karmaşık ve esnek olmayan hale getirir. Bu nedenle, tokenizasyonu çalışma zamanında (runtime) yapacağız. Ancak bu gözlem, özellik/eğitim/çıkarım (FTI) mimarisine çok katı bir şekilde bağlı kalmak zorunda olmadığınızın açık bir örneği olduğu için önemlidir. Bunu alıp kendi kullanım durumunuza uyarlamalısınız.

**Kodların Ayrıntılı Açıklaması**

Bu metinde kod bulunmamaktadır. Ancak bazı teknik terimlerin açıklamaları aşağıda verilmiştir:

- **Feature Store**: Makine öğrenimi modellerinde kullanılan özelliklerin depolanmasını ve yönetilmesini sağlayan merkezi bir sistemdir.
- **Training Pipeline**: Makine öğrenimi modellerinin eğitilmesi için kullanılan verilerin işlenmesi ve modelin eğitilmesi işlemlerini içeren bir dizi adımdır.
- **Inference Pipeline**: Eğitilmiş makine öğrenimi modellerinin yeni veriler üzerinde tahminler yapması için kullanılan işlem hattıdır.
- **LLMs (Large Language Models)**: Büyük miktarda metin verisi üzerinde eğitilen ve doğal dil işleme görevlerinde kullanılan makine öğrenimi modelleridir.
- **RAG (Retrieval-Augmented Generation)**: Metin oluşturma görevlerinde kullanılan ve bilgiyi dış kaynaklardan alıp modele entegre eden bir tekniktir.
- **Vector DB (Vector Database)**: Vektör verilerini depolayan ve sorgulayan bir veritabanıdır. Genellikle makine öğrenimi uygulamalarında kullanılır.
- **Artifact**: Bir işlemin sonucunda ortaya çıkan veri veya dosyadır. Özellik deposunda depolanabilir.
- **Feature Pipeline**: Özelliklerin oluşturulması, işlenmesi ve depolanması için kullanılan işlem hattıdır.
- **Namespace**: Bir dizi ilgili bileşenin veya işlemin mantıksal olarak gruplanmasıdır.
- **Instruct Datasets**: Modelin belirli görevleri yerine getirmesini sağlamak için kullanılan özel bir veri kümesidir.
- **Tokenize**: Metni token adı verilen daha küçük birimlere ayırma işlemidir. Makine öğrenimi modellerinde metin verilerini işlemek için kullanılır.
- **Runtime**: Bir uygulamanın veya kodun çalıştırıldığı ortam veya zaman dilimidir.

---

