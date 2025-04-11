#Asynchronous Inference (Asenkron Çıkarım)

Asenkron çıkarımda, istemci (client) makine öğrenimi (ML) hizmetine bir istek gönderir, bu hizmet isteği onaylar ve işleme için bir kuyruğa (queue) yerleştirir. Gerçek zamanlı çıkarımın (real-time inference) aksine, istemci hemen bir yanıt beklemez. Bunun yerine, makine öğrenimi hizmeti isteği asenkron olarak işler. Bu, mesajları daha sonra makine öğrenimi hizmeti tarafından işlenmek üzere sıraya alan sağlam bir altyapı gerektirir. Sonuçlar hazır olduğunda, bunları istemciye göndermek için birden fazla teknikten yararlanabilirsiniz. Örneğin, sonucun boyutuna bağlı olarak, sonucu ya farklı bir kuyruğa ya da sonuçların depolanmasına ayrılmış bir nesne depolama (object storage) alanına koyabilirsiniz. İstemci, yeni sonuçların olup olmadığını kontrol etmek için bir yoklama mekanizması (polling mechanism) benimseyebilir veya bir itme stratejisi (push strategy) benimseyerek sonuçlar hazır olduğunda istemciyi bilgilendirmek için bir bildirim sistemi uygulayabilir.

Asenkron çıkarım kaynakları daha verimli kullanır. Tüm istekleri aynı anda işlemek zorunda değildir, ancak mesajları işlemek için paralel çalışan makinelerin maksimum sayısını tanımlayabilir. Bu, istekler bir makine tarafından işlenene kadar kuyrukta saklandığından mümkündür. Diğer bir büyük avantajı, zaman aşımı olmadan isteklerdeki ani artışları işleyebilmesidir. Örneğin, bir e-ticaret sitesinde normalde iki makine tarafından işlenen saniyede 10 istek olduğunu varsayalım. Bir promosyon nedeniyle, birçok insan siteyi ziyaret etmeye başladı ve istek sayısı saniyede 100 isteğe yükseldi. Sanal makinelerin (VMs) sayısını 10 kat artırmak yerine, bu da büyük maliyetlere neden olabilir, istekler sıraya alınır ve aynı iki sanal makine bunları başarısız olmadan kendi ritmlerinde işleyebilir.

Asenkron mimarilerin bir diğer popüler avantajı, istenen işin tamamlanması önemli zaman alan durumlarda ortaya çıkar. Örneğin, iş beş dakikadan fazla sürerse, istemciyi yanıt beklemekten alıkoymak istemezsiniz. Asenkron çıkarım önemli avantajlar sunarken, aynı zamanda bazı trade-off'lar da getirir. Daha yüksek gecikme süresi (latency) getirir, bu da zaman duyarlı uygulamalar için daha az uygun olmasını sağlar. Ayrıca, uygulama ve altyapıya karmaşıklık ekler. Tasarım seçimlerinize bağlı olarak, bu mimari tipi çevrimiçi (online) ve çevrimdışı (offline) arasında bir yere düşer, avantajlar ve trade-off'lar arasında bir denge sunar. Örneğin, bu, çıkarımın gecikme süresine çok fazla önem vermediğiniz, ancak maliyetleri ağır bir şekilde optimize etmek istediğiniz sağlam bir tasarımdır. Bu nedenle, belgelerden anahtar kelimelerin çıkarılması, bunları büyük dil modelleri (LLMs) kullanarak özetlemek veya videoların üzerinde deep-fake modelleri çalıştırmak gibi sorunlar için popüler bir seçimdir. Ancak, kuyruktaki istekleri iyi hızlarda işlemek için otomatik ölçeklendirme sistemini (autoscaling system) dikkatlice tasarlarsanız, e-ticaret için çevrimiçi öneriler gibi diğer kullanım durumları için bu tasarımı kullanabilirsiniz. Sonuç olarak, uygulamanızın beklentilerini karşılamak için ne kadar işlem gücü ödemeye istekli olduğunuzu toplar.

**Kod Açıklaması Yok, Metin Çevirisi ve Açıklaması Yapılmıştır.**

Ancak, metinde geçen bazı teknik terimlerin açıklamaları aşağıda verilmiştir:

- **Asynchronous Inference (Asenkron Çıkarım):** Makine öğrenimi modellerinin istekleri eşzamansız olarak işlediği bir çıkarım türüdür.
- **Queue (Kuyruk):** İsteklerin sırayla işlenmesi için kullanılan bir veri yapısıdır.
- **Real-time Inference (Gerçek Zamanlı Çıkarım):** Makine öğrenimi modellerinin istekleri anında işlediği bir çıkarım türüdür.
- **Object Storage (Nesne Depolama):** Büyük miktarda veri depolamak için kullanılan bir depolama türüdür.
- **Polling Mechanism (Yoklama Mekanizması):** İstemcinin sunucuya periyodik olarak yeni veri olup olmadığını sormasıdır.
- **Push Strategy (İtme Stratejisi):** Sunucunun yeni veri hazır olduğunda istemciyi bilgilendirmesidir.
- **Virtual Machines (Sanal Makineler):** Fiziksel makinelerin sanal ortamda simüle edilmiş halleridir.
- **Autoscaling System (Otomatik Ölçeklendirme Sistemi):** Sistemin talebe göre otomatik olarak kaynak ekleyip çıkarmasıdır.
- **LLMs (Büyük Dil Modelleri):** Büyük miktarda metin verisi üzerinde eğitilmiş dil modelleridir.
- **Deep-fake Models (Deep-fake Modelleri):** Gerçekçi ancak sahte resim veya videolar oluşturmak için kullanılan makine öğrenimi modelleridir.

---

