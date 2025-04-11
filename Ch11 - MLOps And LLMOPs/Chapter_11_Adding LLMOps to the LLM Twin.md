#LLM Twin Projesi için CI/CD Pipeline Kurulumu

Önceki bölümde, Docker imajını manuel olarak oluşturarak ve ECR'ye göndererek LLM Twin projesi için altyapıyı nasıl kuracağımızı gördük. Tüm süreci otomatize etmek ve GitHub Actions kullanarak bir CI/CD pipeline ve ZenML kullanarak bir CT pipeline uygulamak istiyoruz. Daha önce de belirtildiği gibi, bir CI/CD/CT pipeline uygulamak, ana dallara gönderilen her özelliğin tutarlı ve test edilmiş olmasını sağlar. Ayrıca, dağıtım ve eğitimi otomatize ederek işbirliğini destekler, zaman kazandırır ve insan hatalarını azaltır. Son olarak, bölümün sonunda, Comet ML'den Opik kullanarak bir prompt izleme pipeline ve ZenML kullanarak bir uyarı sistemi nasıl uygulanacağını göstereceğiz. Bu prompt izleme pipeline, RAG ve LLM mantığını hata ayıklamamıza ve analiz etmemize yardımcı olacaktır. LLM sistemleri deterministik olmayan (non-deterministic) yapıda olduklarından, prompt izlerini yakalamak ve depolamak ML mantığınızı izlemek için önemlidir. Uygulamaya geçmeden önce, LLM Twin'in CI/CD pipeline akışına (flow) kısa bir bölüm ile başlayalım.

İngilizce teknik terimlerin Türkçe karşılıkları parantez içinde verilmiştir:
- CI/CD pipeline (Sürekli Entegrasyon/Sürekli Dağıtım ardışık düzeni)
- CT pipeline (Sürekli Eğitim ardışık düzeni)
- Docker imajı (Docker image)
- ECR (Elastik Container Registry - Esnek Konteyner Kayıt Defteri)
- GitHub Actions (GitHub Eylemleri)
- ZenML (ZenML, makine öğrenimi için bir araç)
- Opik (Opik, Comet ML tarafından sağlanan bir araç)
- Comet ML (Comet ML, makine öğrenimi için bir platform)
- RAG ( Retrieval-Augmented Generation - Alma-Augmentli Üretim)
- LLM (Large Language Model - Büyük Dil Modeli)
- ML (Machine Learning - Makine Öğrenimi)

Kod bulunmamaktadır, ancak metinde geçen bazı teknik terimlerin açıklamaları aşağıda verilmiştir:

- `LLM Twin projesi`: Büyük Dil Modeli ikizi projesi
- `CI/CD pipeline`: Yazılım geliştirme sürecinde, kod değişikliklerinin otomatik olarak test edilmesi, derlenmesi ve üretim ortamına dağıtılmasını sağlayan ardışık düzen.
- `Docker imajı`: Uygulamanın çalışması için gerekli olan her şeyi içeren hafif ve taşınabilir bir konteyner.
- `ECR`: Amazon Web Services tarafından sağlanan, Docker imajlarını depolamak için kullanılan bir kayıt defteri.
- `GitHub Actions`: GitHub tarafından sağlanan, CI/CD pipeline oluşturmak için kullanılan bir araç.
- `ZenML`: Makine öğrenimi projelerinde kullanılan, pipeline oluşturmayı ve yönetmeyi sağlayan bir araç.
- `Opik`: Comet ML tarafından sağlanan, prompt izleme ve analiz için kullanılan bir araç.
- `Comet ML`: Makine öğrenimi projelerinde kullanılan, deneyleri takip etmek ve analiz etmek için kullanılan bir platform.
- `RAG`: Retrieval-Augmented Generation, bir metin üretim tekniğidir.
- `LLM`: Büyük Dil Modeli, doğal dil işleme görevlerinde kullanılan bir makine öğrenimi modelidir.
- `ML`: Makine Öğrenimi, bilgisayarların veri üzerinden öğrenmesini sağlayan bir bilim dalıdır.

---

