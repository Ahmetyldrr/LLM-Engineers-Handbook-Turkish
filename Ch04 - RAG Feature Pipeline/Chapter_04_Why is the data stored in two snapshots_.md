#Veri Depolama Tasarımı
Verilerimizin iki anlık görüntüsünü (snapshot) mantıksal özellik deposunda (logical feature store) saklıyoruz: 
Veriler temizlendikten sonra (After the data is cleaned) : İnce ayar için (fine-tuning) LLM'ler (LLMs) 
Dokümanlar parçalandığında (chunked) ve gömüldükten (embedded) sonra : RAG için 
Bunu neden bu şekilde tasarladık? 
Özelliklere yalnızca eğitim (training) ve çıkarım (inference) için özellik deposundan erişilmesi gerektiğini unutmayın. 
Böylece tasarımımıza tutarlılık kazandırır ve daha temiz hale getirir. 
Ayrıca, ince ayar (fine-tuning) ve gömme (embedding) kullanım durumumuz için özel olarak temizlenen verileri MongoDB veri ambarında (data warehouse) saklamak bir antipattern (anti-pattern) olurdu. 
Depodan gelen veriler şirket genelinde paylaşılır. 
Bu nedenle, belirli bir kullanım durumu için işlenmesi iyi bir uygulama değildir. 
Verileri farklı şekilde temizlememiz ve ön işlemden geçirmemiz gereken başka bir özetleme (summarization) kullanım durumunu düşünün. 
Kullanım durumu adıyla ön ekli (prefixed) yeni bir "Temizlenmiş Veri" tablosu oluşturmalıyız. 
Her yeni kullanım durumu için bunu tekrarlamak zorundayız. 
Bu nedenle, bir spagetti veri ambarına (spaghetti data warehouse) sahip olmaktan kaçınmak için, veri ambarından gelen veriler genel (generic) tutulur ve yalnızca aşağı akış bileşenlerinde (downstream components) belirli uygulamalara (specific applications) modellenir, ki bizim durumumuzda bu özellik deposudur (feature store). 
Nihayetinde, Çekirdek adımlar (Core steps) bölümünde bahsettiğimiz gibi, bir vektör veritabanının (vector DB) meta veri dizinini (metadata index) bir NoSQL veritabanı (NoSQL DB) olarak kullanabilirsiniz. 
Bu faktörlere dayanarak, temizlenmiş verileri, parçalanmış ve gömülmüş doküman sürümleriyle birlikte Qdrant'ta saklamaya karar verdik. 
Hızlı bir hatırlatma olarak, LLM Twin sistemimizi operasyon haline getirirken, 5. Bölüm'de açıklanan create instruct veri kümesi (dataset) ardışık düzeni (pipeline), Qdrant'tan temizlenmiş dokümanları okuyacak, işleyecek ve versionlanmış bir ZenML artifact'i altında kaydedecektir. 
Eğitim ardışık düzeni (training pipeline) bir veri kümesi gerektirir, düz dokümanlar değil. 
Bu, mantıksal özellik deposunun (logical feature store), çevrimiçi hizmet (online serving) için Qdrant vektör veritabanını (Qdrant vector DB) ve çevrimdışı eğitim (offline training) için ZenML artifact'lerini içerdiğinin bir hatırlatmasıdır.

İngilizce Teknik Terimler Parantez İçinde:
#Veri Depolama Tasarımı (Data Storage Design)
Verilerimizin iki anlık görüntüsünü (snapshot) mantıksal özellik deposunda (logical feature store) saklıyoruz: 
Veriler temizlendikten sonra (After the data is cleaned) : İnce ayar için (fine-tuning) LLM'ler (LLMs - Large Language Models) 
Dokümanlar parçalandığında (chunked) ve gömüldükten (embedded) sonra : RAG (RAG - Retrieval-Augmented Generation) için 
Bunu neden bu şekilde tasarladık? 
Özelliklere yalnızca eğitim (training) ve çıkarım (inference) için özellik deposundan (feature store) erişilmesi gerektiğini unutmayın. 
Böylece tasarımımıza tutarlılık kazandırır ve daha temiz hale getirir. 
Ayrıca, ince ayar (fine-tuning) ve gömme (embedding) kullanım durumumuz için özel olarak temizlenen verileri MongoDB veri ambarında (data warehouse) saklamak bir antipattern (anti-pattern) olurdu. 
Depodan gelen veriler şirket genelinde paylaşılır. 
Bu nedenle, belirli bir kullanım durumu için işlenmesi iyi bir uygulama değildir. 
Verileri farklı şekilde temizlememiz ve ön işlemden geçirmemiz gereken başka bir özetleme (summarization) kullanım durumunu düşünün. 
Kullanım durumu adıyla ön ekli (prefixed) yeni bir "Temizlenmiş Veri" tablosu oluşturmalıyız. 
Her yeni kullanım durumu için bunu tekrarlamak zorundayız. 
Bu nedenle, bir spagetti veri ambarına (spaghetti data warehouse) sahip olmaktan kaçınmak için, veri ambarından gelen veriler genel (generic) tutulur ve yalnızca aşağı akış bileşenlerinde (downstream components) belirli uygulamalara (specific applications) modellenir, ki bizim durumumuzda bu özellik deposudur (feature store). 
Nihayetinde, Çekirdek adımlar (Core steps) bölümünde bahsettiğimiz gibi, bir vektör veritabanının (vector DB) meta veri dizinini (metadata index) bir NoSQL veritabanı (NoSQL DB) olarak kullanabilirsiniz. 
Bu faktörlere dayanarak, temizlenmiş verileri, parçalanmış ve gömülmüş doküman sürümleriyle birlikte Qdrant'ta (Qdrant - bir vektör veritabanı) saklamaya karar verdik. 
Hızlı bir hatırlatma olarak, LLM Twin sistemimizi operasyon haline getirirken, 5. Bölüm'de açıklanan create instruct veri kümesi (dataset) ardışık düzeni (pipeline), Qdrant'tan temizlenmiş dokümanları okuyacak, işleyecek ve versionlanmış bir ZenML artifact'i (ZenML artifact - bir veri saklama birimi) altında kaydedecektir. 
Eğitim ardışık düzeni (training pipeline) bir veri kümesi gerektirir, düz dokümanlar değil. 
Bu, mantıksal özellik deposunun (logical feature store), çevrimiçi hizmet (online serving) için Qdrant vektör veritabanını (Qdrant vector DB) ve çevrimdışı eğitim (offline training) için ZenML artifact'lerini içerdiğinin bir hatırlatmasıdır.

Kodların Ayrıntılı Açıklaması:
Bu metinde kod bulunmamaktadır. Ancak, veri akışı ve işlemleri aşağıdaki şekilde açıklanabilir:

1. `Veriler temizlenir` (Data Cleaning): Ham veriler temizlenir ve işlenir.
2. `Temizlenmiş veriler Qdrant'a saklanır` (Storing Cleaned Data in Qdrant): Temizlenmiş veriler Qdrant vektör veritabanına saklanır.
3. `Dokümanlar parçalanır ve gömülür` (Chunking and Embedding Documents): Dokümanlar parçalanır ve gömülür.
4. `Parçalanmış ve gömülmüş dokümanlar Qdrant'a saklanır` (Storing Chunked and Embedded Documents in Qdrant): Parçalanmış ve gömülmüş dokümanlar Qdrant'a saklanır.
5. `Create instruct veri kümesi ardışık düzeni` (Create Instruct Dataset Pipeline): Qdrant'tan temizlenmiş dokümanları okur, işler ve versionlanmış bir ZenML artifact'i altında kaydeder.
6. `Eğitim ardışık düzeni` (Training Pipeline): Versionlanmış ZenML artifact'ini kullanarak model eğitir.

Bu adımlar, LLM Twin sisteminin operasyon haline getirilmesinde önemli bir rol oynar.

---

