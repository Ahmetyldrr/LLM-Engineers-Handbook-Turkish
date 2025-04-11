# Önceden Alınan Adımlar (Pre-retrieval Steps)

Önceden alınan adımlar iki farklı şekilde gerçekleştirilir: 
Veri indeksleme (Data Indexing): RAG ingestion pipeline'ın bir parçasıdır. Temelde, verileri daha iyi indekslemek için temizleme (cleaning) veya parçalama (chunking) modüllerinde uygulanır.
Sorgu optimizasyonu (Query Optimization): Kullanıcının sorgusunu embedding yapmadan ve vektör DB'den (vector DB) parçaları (chunks) almadan önce doğrudan sorgu üzerinde gerçekleştirilir.

Verileri embedding'ler kullanarak indekslediğimiz için (semantik olarak bir belgeyi temsil eden chunk'lar), çoğu veri indeksleme tekniği, veri ön işleme (preprocessing) ve yapılandırma (structuring) üzerine odaklanır. Bu, geri alma verimliliğini (retrieval efficiency) artırmak içindir. 
Örneğin:
- Kaydırma penceresi (Sliding Window): Kaydırma penceresi tekniği, metin chunk'ları arasında örtüşme (overlap) sağlar. Bu, chunk sınırlarına yakın önemli bağlamın (context) korunmasını sağlar ve geri alma doğruluğunu (retrieval accuracy) artırır. Bu, özellikle yasal belgeler, bilimsel makaleler, müşteri destek günlükleri ve tıbbi kayıtlar gibi alanlarda faydalıdır. Burada kritik bilgiler genellikle birden fazla bölüme yayılmıştır. Embedding, chunk ve örtüşen kısmı birlikte hesaplar. Böylece, kaydırma penceresi, sınırlar boyunca bağlamı koruyarak sistemin ilgili ve tutarlı bilgileri almasını sağlar.

- Veri ayrıntı düzeyini artırma (Enhancing Data Granularity): Bu, alakasız ayrıntıları temizleme, olgusal doğruluğu (factual accuracy) doğrulama ve güncel olmayan bilgileri güncelleme gibi veri temizleme tekniklerini içerir. Temiz ve doğru bir veri seti, daha keskin bir geri almayı sağlar.

- Meta veri (Metadata): Tarihler, URL'ler, harici kimlikler veya bölüm işaretleri gibi meta veri etiketleri eklemek, geri alma sırasında sonuçların verimli bir şekilde filtrelenmesine yardımcı olur.

- İndeks yapılarını optimize etme (Optimizing Index Structures): Farklı veri indeksleme yöntemlerine, çeşitli chunk boyutlarına ve çoklu indeksleme stratejilerine dayanır. 
Küçükten büyüğe (Small-to-big): Algoritma, geri alma için kullanılan chunk'ları ve nihai cevap oluşturma için kullanılan bağlamı ayırır. Algoritma, daha küçük bir metin dizisini embedding hesaplamak için kullanırken, diziyi ve çevresindeki daha geniş bir pencereyi meta veride tutar. Böylece, daha küçük chunk'lar kullanmak geri almanın doğruluğunu artırırken, daha geniş bağlam daha fazla bağlamsal bilgi ekler. Bunun arkasındaki sezgisel düşünce, eğer tüm metni embedding hesaplamak için kullanırsak, çok fazla gürültü (noise) ekleyebiliriz veya metin birden fazla konu içerebilir, bu da genel anlamsal temsilin (semantic representation) zayıf olmasına neden olur.

# Şekil 4.6: Sorgu Yönlendirme (Query Routing)

Sorgu optimizasyonu tarafında, LLM için alınan bilgileri daha da iyileştirmek için sorgu yönlendirme (query routing), sorgu yeniden yazma (query rewriting) ve sorgu genişletme (query expansion) gibi tekniklerden yararlanabiliriz:
- Sorgu Yönlendirme (Query Routing): Kullanıcının girdisine göre, farklı veri kategorileriyle etkileşime girmemiz ve her kategoriyi farklı şekilde sorgulamamız gerekebilir. Sorgu yönlendirme, kullanıcının girdisine göre ne yapılacağına karar vermek için kullanılır; bu, eğer/aksi halde (if/else) ifadelerine benzer, ancak kararlar tamamen doğal dil kullanılarak verilir. Şekil 4.6'da gösterildiği gibi, varsayalım ki kullanıcının girdisine dayanarak, RAG yapmak için, vektör arama sorguları (vector search queries) kullanarak bir vektör DB'den, kullanıcı sorgusunu SQL komutuna çevirerek standart bir SQL DB'den veya REST API çağrıları kullanarak internetten ek bağlam alabiliriz. Sorgu yönlendirici, bir bağlamın gerekli olup olmadığını da algılayabilir, bu da bize dış veri depolama alanlarına gereksiz çağrılar yapmaktan kaçınmamıza yardımcı olur. Ayrıca, sorgu yönlendirici, belirli bir girdi için en iyi istem şablonunu (prompt template) seçmek için kullanılabilir. Örneğin, LLM Twin kullanım durumunda, kullanıcının bir makale paragrafı, bir gönderi veya bir kod parçası isteyip istemediğine bağlı olarak, oluşturma sürecini optimize etmek için farklı istem şablonlarına ihtiyaç duyulur. Yönlendirme genellikle, hangi yolu izleyeceğine karar vermek için bir LLM kullanır veya en benzer vektörlere sahip yolu seçerek embedding'ler kullanır. Özetle, sorgu yönlendirme, eğer/aksi halde ifadesine benzer, ancak doğal dil ile çalıştığı için çok daha esnektir.

- Sorgu Yeniden Yazma (Query Rewriting): Bazen kullanıcının ilk sorgusu, verilerinizin yapısına mükemmel şekilde uymayabilir. Sorgu yeniden yazma, soruyu indekslenmiş bilgilere daha iyi uyacak şekilde yeniden formüle ederek bu sorunu çözer. Bu, aşağıdaki teknikleri içerebilir:
  - Parafraze etme (Paraphrasing): Kullanıcının sorgusunu anlamını koruyarak yeniden ifade etme (örneğin, “İklim değişikliğinin nedenleri nelerdir?” “küresel ısınmaya katkıda bulunan faktörler” olarak yeniden yazılabilir).
  - Eş anlamlı kelime değiştirme (Synonym Substitution): Arama kapsamını genişletmek için daha az kullanılan kelimeleri eş anlamlılarıyla değiştirme (örneğin, “mutlu” “neşeli” olarak yeniden yazılabilir).
  - Alt sorgular (Sub-queries): Daha uzun sorguları birden fazla kısa ve odaklanmış alt sorgulara ayırma. Bu, geri alma aşamasının ilgili belgeleri daha kesin olarak tanımlamasına yardımcı olabilir.
  - Varsayımsal belge embedding'leri (Hypothetical Document Embeddings - HyDE): Bu teknik, bir LLM'nin sorguya varsayımsal bir yanıt oluşturmasını içerir. Ardından, hem orijinal sorgu hem de LLM'nin yanıtı geri alma aşamasına beslenir.

- Sorgu Genişletme (Query Expansion): Bu yaklaşım, kullanıcının sorusunu ek terimler veya kavramlar ekleyerek zenginleştirmeyi amaçlar, bu da aynı ilk sorunun farklı bakış açılarını ortaya koyar. Örneğin, “hastalık” ararken, orijinal sorgu kelimeleriyle ilişkili eş anlamlıları ve ilgili terimleri kullanarak “hastalıklar” veya “semptomlar” da dahil edilebilir.

- Kendi Kendine Sorgu (Self-query): Temel fikir, yapılandırılmamış sorguları yapılandırılmış olanlara eşlemektir. Bir LLM, girdi metni içindeki önemli varlıkları, olayları ve ilişkileri tanımlar. Bu tanımlamalar, vektör arama alanını daraltmak için filtreleme parametreleri olarak kullanılır (örneğin, sorgu içinde “Paris” gibi şehirleri tanımlamak ve vektör arama alanınızı daraltmak için filtreye eklemek).

Hem veri indeksleme hem de sorgu optimizasyonu, geri alma öncesi optimizasyon teknikleri olarak, veri türünüze, yapınıza ve kaynağınıza büyük ölçüde bağlıdır. Bu nedenle, herhangi bir veri işleme hattında olduğu gibi, her zaman işe yarayan tek bir yöntem yoktur; çünkü her kullanım senaryosunun kendine özgü özellikleri ve tuzakları vardır. Geri alma öncesi RAG katmanınızı optimize etmek deneyseldir. Bu nedenle, önemli olan, bu bölümde sıralananlar gibi birden fazla yöntemi denemek, yinelemek ve neyin en iyi sonucu verdiğini gözlemlemektir.

Kod açıklamaları:
Yukarıdaki metinde kod bulunmamaktadır. Ancak, örnek olması açısından, basit bir sorgu yönlendirme (query routing) işlemi için Python kodu aşağıdaki gibi olabilir:

```python
def query_routing(user_input):
    # Örnek sorgu yönlendirme mantığı
    if "SQL" in user_input:
        # SQL DB sorgusu yap
        return sql_query(user_input)
    elif "vector search" in user_input:
        # Vektör DB sorgusu yap
        return vector_search_query(user_input)
    else:
        # Varsayılan işlemleri yap
        return default_operation(user_input)

def sql_query(query):
    # SQL sorgusu oluştur ve çalıştır
    sql_command = translate_to_sql(query)
    return execute_sql(sql_command)

def vector_search_query(query):
    # Vektör arama sorgusu oluştur ve çalıştır
    vector_query = create_vector_query(query)
    return execute_vector_search(vector_query)

def default_operation(query):
    # Varsayılan işlem
    return "Varsayılan işlem sonucu"

# Kullanıcı girdisi
user_input = "SQL ile ilgili sorgu"

# Sorgu yönlendirme fonksiyonunu çağır
result = query_routing(user_input)
print(result)
```

Bu kod, basit bir sorgu yönlendirme mantığını gösterir. Kullanıcı girdisine göre farklı işlemler yapar. Gerçek uygulamalarda, bu mantık daha karmaşık olabilir ve doğal dil işleme (NLP) tekniklerini içerebilir.

---

