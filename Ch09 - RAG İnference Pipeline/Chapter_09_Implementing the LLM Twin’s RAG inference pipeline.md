#RAG Çıkarım İşlem Hattının Uygulanması

Bu bölümün başında açıklandığı gibi, RAG çıkarım işlem hattı (RAG inference pipeline) temel olarak üç bölüme ayrılabilir: erişim modülü (retrieval module), istem oluşturma (prompt creation) ve cevap üretme (answer generation), bu da artırılmış istem (augmented prompt) ile bir LLM (Large Language Model) çağırma anlamına gelir. Bu bölümde, birincil odak noktamız, kod ve mantığın çoğunun bulunduğu erişim modülünü (retrieval module) uygulamak olacaktır. Ardından, alınan içerik (retrieved context) ve kullanıcı sorgusu (user query) kullanılarak son istemin (final prompt) nasıl oluşturulacağına bakacağız. Son olarak, erişim modülünü (retrieval module), istem oluşturma mantığını (prompt creation logic) ve LLM'i birleştirerek uçtan uca bir RAG iş akışını (end-to-end RAG workflow) yakalamayı inceleyeceğiz. Ne yazık ki, ince-tuned LLM Twin modülümüzü AWS SageMaker'a dağıtana kadar LLM'i test edemeyeceğiz, bu nedenle bu bölümün sonunda, ancak Bölüm 10'u bitirdikten sonra uçtan uca test edebileceğiniz RAG çıkarım işlem hattını (RAG inference pipeline) nasıl uygulayacağınızı öğreneceksiniz. Şimdi, erişim modülünün (retrieval module) uygulanmasına bakalım.

İngilizce Teknik Terimler Parantez İçinde Eklenmiştir:
#RAG Çıkarım İşlem Hattının Uygulanması

Bu bölümün başında açıklandığı gibi, RAG çıkarım işlem hattı temel olarak üç bölüme ayrılabilir: erişim modülü, istem oluşturma (prompt creation) ve cevap üretme (answer generation), bu da artırılmış istem (augmented prompt) ile bir Büyük Dil Modeli (LLM - Large Language Model) çağırma anlamına gelir. Bu bölümde, birincil odak noktamız, kod ve mantığın çoğunun bulunduğu erişim modülünü uygulamak olacaktır. Ardından, alınan içerik (retrieved context) ve kullanıcı sorgusu (user query) kullanılarak son istemin (final prompt) nasıl oluşturulacağına bakacağız. Son olarak, erişim modülünü, istem oluşturma mantığını (prompt creation logic) ve Büyük Dil Modeli'ni (LLM) birleştirerek uçtan uca bir RAG iş akışını (end-to-end RAG workflow) yakalamayı inceleyeceğiz. Ne yazık ki, İnce ayarlı (fine-tuned) Büyük Dil Modeli İkiz (LLM Twin) modülümüzü AWS SageMaker'a dağıtana kadar Büyük Dil Modeli'ni (LLM) test edemeyeceğiz, bu nedenle bu bölümün sonunda, ancak Bölüm 10'u bitirdikten sonra uçtan uca test edebileceğiniz RAG çıkarım işlem hattını nasıl uygulayacağınızı öğreneceksiniz. Şimdi, erişim modülünün uygulanmasına bakalım.

Kod bulunmamaktadır, ancak metin ayrıntılı olarak açıklanmıştır.

---

