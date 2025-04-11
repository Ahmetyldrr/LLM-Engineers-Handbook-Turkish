#Bu Bölümde Neler Öğrendik
In this chapter, we’ve learned how to design and build the data collection pipeline for the LLM Twin use case. Instead of relying on static datasets, we collected our custom data to mimic real-world situations, preparing us for real-world challenges in building AI systems. First, we examined the architecture of LLM Twin’s data collection pipeline, which functions as an ETL (Extract, Transform, Load) process. Next, we started digging into the pipeline implementation. We began by understanding how we can orchestrate the pipeline using ZenML. Then, we looked into the crawler implementation. We learned how to crawl data in three ways: using CLI (Command-Line Interface) commands in subprocesses or using utility functions from LangChain or Selenium to build custom logic that programmatically manipulates the browser. Finally, we looked into how to build our own ODM (Object Document Mapping) class, which we used to define our document class hierarchy, which contains entities such as articles, posts, and repositories. At the end of the chapter, we learned how to run ZenML pipelines with different YAML (YAML Ain't Markup Language) configuration files and explore the results in the dashboard. We also saw how to interact with the MongoDB data warehouse through the ODM classes. In the next chapter, we will cover the key steps of the RAG (Retrieval-Augmented Generation) feature pipeline, including chunking and embedding documents, ingesting these documents into a vector DB (Database), and applying pre-retrieval optimizations to improve performance. We will also set up the necessary infrastructure programmatically using Pulumi and conclude by deploying the RAG ingestion pipeline to AWS (Amazon Web Services).

**Kodların Ayrıntılı Açıklaması:**

Bu bölümde kod bulunmamaktadır, ancak metinde geçen teknik terimlerin açıklamaları aşağıda verilmiştir:

*   ETL (Extract, Transform, Load): Verileri farklı kaynaklardan toplama, işleme ve bir hedefe yükleme sürecidir.
*   ZenML: Makine öğrenimi işlemlerini otomatize etmek için kullanılan bir araçtır. Pipeline'ları yönetmek için kullanılır.
*   CLI (Command-Line Interface): Kullanıcıların komutları girmesini sağlayan bir arayüzdür.
*   LangChain: Doğal dil işleme görevleri için kullanılan bir kütüphanedir.
*   Selenium: Web tarayıcılarını programatik olarak kontrol etmek için kullanılan bir araçtır.
*   ODM (Object Document Mapping): Nesne yönelimli programlamada kullanılan bir tekniktir. Veritabanındaki belgeleri (document) nesnelere eşler.
*   YAML (YAML Ain't Markup Language): İnsan tarafından okunabilir bir veri serileştirme formatıdır. ZenML pipeline'larının yapılandırılması için kullanılır.
*   MongoDB: NoSQL bir veritabanıdır. Büyük miktarda veri depolamak için kullanılır.
*   RAG (Retrieval-Augmented Generation): Metin oluşturma görevlerinde kullanılan bir tekniktir. Bilgi getirme (retrieval) ve oluşturma (generation) adımlarını birleştirir.
*   Vector DB (Database): Vektörleri depolamak için optimize edilmiş bir veritabanıdır. Benzerlik aramaları için kullanılır.
*   Pulumi: Altyapıyı kod olarak tanımlamaya olanak tanıyan bir platformdur. Bulut kaynaklarını programatik olarak yönetmek için kullanılır.
*   AWS (Amazon Web Services): Amazon tarafından sunulan bir bulut bilişim platformudur. Uygulamaları deploy etmek için kullanılır.

Bu teknik terimlerin açıklamaları, metinde geçen kavramların daha iyi anlaşılmasına yardımcı olacaktır.

---

