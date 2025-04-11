#Bu Bölümde Öğrendiklerimiz

Bu bölüm, gelişmiş bir RAG (Retrieval-Augmented Generation) çıkarım (inference) hattı oluşturmayı öğretti. RAG sisteminin yazılım mimarisine (software architecture) göz atarak başladık. Ardından, retrieval modülü içinde kullandığımız gelişmiş RAG yöntemlerine (advanced RAG methods) odaklandık, örneğin sorgu genişletme (query expansion), kendi kendine sorgulama (self-querying), filtrelenmiş vektör arama (filtered vector search) ve yeniden sıralama (reranking) gibi. Daha sonra, tüm gelişmiş RAG bileşenlerini (advanced RAG components) tek bir arayüz (interface) altında birleştiren modüler bir ContextRetriever sınıfı yazmayı gördük, bu sayede ilgili belgeleri (relevant documents) aramayı kolaylaştırdık. Son olarak, retrieval, istem artırma (prompt augmentation) ve LLM çağrısı (LLM call) gibi eksik noktaları, RAG çıkarım hattımız (RAG inference pipeline) olarak görev yapacak tek bir RAG işlevi altında nasıl bağlayacağımızı inceledik. Bu bölümde birkaç kez vurgulandığı gibi, ince ayarlı LLM'imizi (fine-tuned LLM) AWS SageMaker'a bir çıkarım uç noktası (inference endpoint) olarak dağıtmadığımız için test edemedik. Bu nedenle, bir sonraki bölümde, LLM'i AWS SageMaker'a nasıl dağıtacağımızı, uç noktayı çağırmak için bir çıkarım arayüzü (inference interface) yazmayı ve iş katmanımız (business layer) olarak görev yapacak bir FastAPI web sunucusu (FastAPI web server) uygulamasını öğreneceğiz.

İngilizce teknik terimlerin Türkçeleri parantez içinde verildi:
- RAG (Retrieval-Augmented Generation)
- inference (çıkarım)
- software architecture (yazılım mimarisi)
- advanced RAG methods (gelişmiş RAG yöntemleri)
- query expansion (sorgu genişletme)
- self-querying (kendi kendine sorgulama)
- filtered vector search (filtrelenmiş vektör arama)
- reranking (yeniden sıralama)
- interface (arayüz)
- relevant documents (ilgili belgeler)
- prompt augmentation (istem artırma)
- LLM call (LLM çağrısı)
- fine-tuned LLM (ince ayarlı LLM)
- inference endpoint (çıkarım uç noktası)
- inference interface (çıkarım arayüzü)
- business layer (iş katmanı)
- FastAPI web server (FastAPI web sunucusu)

Kod bulunmamaktadır, dolayısıyla kod açıklaması da bulunmamaktadır.

---

