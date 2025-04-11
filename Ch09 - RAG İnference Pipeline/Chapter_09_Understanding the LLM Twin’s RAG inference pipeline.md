#RAG Çıkarım İşlem Hattı (RAG Inference Pipeline) Mimarisine Genel Bakış

RAG çıkarım işlem hattını uygulamadan önce, yazılım mimarisini ve gelişmiş RAG tekniklerini tartışmak istiyoruz. Şekil 9.1, RAG çıkarım akışına genel bir bakış sunmaktadır. Çıkarım işlem hattı, girdi sorgusuyla (input query) başlar, alma modülü (retrieval module) kullanarak bağlamı (context) alır ve son yanıtı oluşturmak için LLM SageMaker hizmetini çağırır.

Şekil 9.1: RAG Çıkarım İşlem Hattı Mimarisini gösterir.

Özellik işlem hattı (feature pipeline) ve alma modülü, Şekil 9.1'de tanımlandığı gibi, bağımsız süreçlerdir. Özellik işlem hattı, vektör veritabanını (vector DB) doldurmak için bir program dahilinde farklı bir makinede çalışır. Aynı zamanda, alma modülü, her kullanıcı isteğinde çıkarım işlem hattı içinde talep üzerine çağrılır. İki bileşen arasındaki endişeleri ayırarak, vektör veritabanı her zaman en son verilerle doldurulur, özellik tazeliğini (feature freshness) sağlarken, alma modülü her istekte en son özelliklere erişebilir.

RAG alma modülünün girdisi, kullanıcının sorgusudur; buna dayanarak, vektör veritabanından en alakalı ve benzer veri noktalarını döndürmemiz gerekir, bu da LLM'nin son yanıtı oluşturmasında yönlendirici olacaktır.

RAG çıkarım işlem hattının dinamiklerini tam olarak anlamak için, Şekil 9.1'deki mimari akışı adım adım inceleyelim:

1. **Kullanıcı Sorgusu (User Query)**: Kullanıcının bir sorgu yapmasıyla başlarız, örneğin “Bir makale yaz...”

2. **Sorgu Genişletme (Query Expansion)**: İlk sorguyu genişleterek, orijinal kullanıcı sorgusunun farklı yönlerini veya yorumlarını yansıtan birden fazla sorgu oluştururuz. Böylece, bir sorgu yerine xN sorgusu kullanırız. Arama terimlerini çeşitlendirerek, alma modülü ilgili veri noktalarının kapsamlı bir kümesini yakalama olasılığını artırır. Bu adım, orijinal sorgu çok dar veya belirsiz olduğunda çok önemlidir.

3. **Kendi Kendine Sorgulama (Self-Querying)**: Orijinal sorgudan yazarın adı gibi yararlı meta verileri (metadata) çıkarırız. Çıkarılan meta veriler, vektör arama işleminde filtre olarak kullanılacak ve sorgu vektör uzayından (query vector space) gereksiz veri noktalarını ortadan kaldıracaktır (arama daha doğru ve hızlı hale gelir).

4. **Filtrelenmiş Vektör Arama (Filtered Vector Search)**: Her sorguyu gömerek (embedding) ve benzerlik araması yaparak her aramanın üst K veri noktasını buluruz. Genişletilmiş sorguların sayısına karşılık gelen xN araması gerçekleştiririz. Bu adımı filtrelenmiş vektör araması olarak adlandırıyoruz çünkü kendi kendine sorgulama adımından çıkarılan meta verileri sorgu filtreleri olarak kullanıyoruz.

5. **Sonuçları Toplama (Collecting Results)**: Her arama işlemi için spesifik genişletilmiş sorgu yorumuna en yakın olan xK sonuç elde ederiz. Ayrıca, tüm xN aramalarının sonuçlarını toplarız ve N x K sonuç içeren makaleler, gönderiler ve depo parçaları (repository chunks) karışımından oluşan bir liste ile sonuçlanırız. Sonuçlar, orijinal sorgunun farklı yönlerine dayanan daha geniş bir potansiyel ilgili parçalar kümesi içerir.

6. **Yeniden Sıralama (Reranking)**: N x K potansiyel öğeler listesinden yalnızca en alakalı K sonuçlarını korumak için listeyi daha da filtrelememiz gerekir. Her parçayı ilk kullanıcı sorgusuna göre alaka ve önemine göre puanlayan bir yeniden sıralama algoritması kullanacağız. Puanı hesaplamak için bir nöral çapraz kodlayıcı (neural cross-encoder) modeli kullanacağız; bu, 0 ile 1 arasında bir değerdir; burada 1, sonucun sorgu ile tamamen alakalı olduğu anlamına gelir. Son olarak, N x K sonuçlarını puana göre sıralar ve üst K öğeleri seçeriz. Böylece, çıktı, en alakalı veri noktalarının üstte olduğu K parçalarının sıralı bir listesidir.

7. **Prompt Oluşturma ve LLM Çağrısı (Build the Prompt and Call the LLM)**: En alakalı K parçalarının son listesini, son promptu oluşturmak için kullanılan bir diziye eşleriz. Promptu, bir prompt şablonu, alınan bağlam ve kullanıcının sorgusunu kullanarak oluştururuz. Sonunda, artırılmış prompt, bir API uç noktası olarak AWS SageMaker üzerinde barındırılan LLM'ye gönderilir.

8. **Cevap (Answer)**: Cevabın oluşturulmasını bekleriz. LLM, promptu işledikten sonra, RAG mantığı, oluşturulan yanıtı kullanıcıya göndererek sona erer.

Bu, RAG çıkarım işlem hattının genel bakışını tamamlar. Şimdi, ayrıntılara daha derinlemesine bakalım.

Kodların ayrıntılı açıklaması:

Yukarıdaki metinde kod bulunmamaktadır, ancak RAG çıkarım işlem hattının adımlarını gerçekleştirmek için kullanılabilecek bazı kod parçaları aşağıda verilmiştir:

1. **Sorgu Genişletme (Query Expansion)**:
   ```python
   # Örnek olarak, birden fazla sorgu oluşturmak için
   def query_expansion(original_query, num_queries):
       # Genişletilmiş sorguları oluşturmak için bir yöntem kullanın
       expanded_queries = []
       for i in range(num_queries):
           expanded_query = f"{original_query} {i}"
           expanded_queries.append(expanded_query)
       return expanded_queries
   ```

2. **Kendi Kendine Sorgulama (Self-Querying)**:
   ```python
   # Meta verileri sorgudan çıkarmak için
   def self_querying(query):
       # Meta verileri çıkarmak için bir yöntem kullanın
       metadata = {}
       # Örneğin, yazarın adını sorgudan çıkarma
       metadata['author'] = 'Yazarın Adı'
       return metadata
   ```

3. **Filtrelenmiş Vektör Arama (Filtered Vector Search)**:
   ```python
   # Sorguları gömmek ve benzerlik araması yapmak için
   def filtered_vector_search(expanded_queries, metadata):
       # Gömme işlemini gerçekleştirmek için bir model kullanın
       # Benzerlik araması için bir yöntem kullanın
       results = []
       for query in expanded_queries:
           # Filtrelenmiş arama sonuçlarını alın
           result = search(query, metadata)
           results.extend(result)
       return results
   ```

4. **Yeniden Sıralama (Reranking)**:
   ```python
   # Sonuçları yeniden sıralamak için
   def reranking(results, original_query):
       # Nöral çapraz kodlayıcı modelini kullanarak puanları hesaplayın
       scores = []
       for result in results:
           score = compute_score(result, original_query)
           scores.append(score)
       # Sonuçları puana göre sıralayın
       sorted_results = sorted(zip(results, scores), key=lambda x: x[1], reverse=True)
       return sorted_results
   ```

Bu kod parçaları, RAG çıkarım işlem hattının bazı adımlarını gerçekleştirmek için örnek olarak verilmiştir. Gerçek uygulama, spesifik gereksinimlere ve kullanılan teknolojilere bağlı olarak değişecektir.

---

