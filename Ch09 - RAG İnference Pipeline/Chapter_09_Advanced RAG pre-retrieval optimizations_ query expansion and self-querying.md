#Ön-Alma Optimizasyonu Adımını Optimize Etme

    Aşağıdaki ingilizce paragrafı bire bir türkçeye çevirisi:
    Ön-alma optimizasyonu adımını optimize etmek için iki yöntem uyguladık: sorgu genişletme (query expansion) ve kendi kendine sorgulama (self-querying). İki yöntem, filtrelenmiş vektör arama (filtered vector search) adımıyla yakından çalışır, ki buna sonraki bölümde değineceğiz. Ancak şimdilik, sorgu genişletme için kodu anlamakla başlayacağız ve kendi kendine sorgulamayı uygulamaya geçeceğiz. Bu iki yöntem içinde, sorgu genişletme adımında orijinal sorgunun varyasyonlarını oluşturmak ve kendi kendine sorgulama algoritması içinde gerekli meta verileri (metadata) çıkarmak için OpenAI'ın API'sini kullanacağız. Bu kitabı yazdığımızda, tüm örneklerimizde GPT-4o-mini kullandık, ancak OpenAI'ın modelleri hızla gelişiyor, bu nedenle modelin kullanımdan kaldırılması muhtemel. Ancak bu bir sorun değil, çünkü OPENAI_MODEL_ID ortam değişkenini (environment variable) yapılandırarak .env dosyasındaki modeli hızlı bir şekilde değiştirebilirsiniz.

İngilizce paragraf:
```markdown
We implemented two methods to optimize the pre-retrieval optimization step: query expansion and self-querying. The two methods work closely with the filtered vector search step, which we will touch on in the next section. For now, however, we will start with understanding the code for query expansion and move to implementing self-querying. Within these two methods, we will leverage OpenAI’s API to generate variations of the original query within the query expansion step and to extract the necessary metadata within the self-querying algorithm. When we wrote this book, we used GPT-4o-mini in all our examples, but as OpenAI’s models quickly evolve, the model might get deprecated. But that’s not an issue, as you can quickly change it in your .env file by configuring the OPENAI_MODEL_ID environment variable.
```

Kodların ayrıntılı açıklaması:
Paragraf içinde kod bulunmamaktadır, ancak `.env` dosyası ve `OPENAI_MODEL_ID` ortam değişkeni ile ilgili bilgiler mevcuttur.

- `.env` dosyası: Çevre değişkenlerini saklamak için kullanılan bir dosyadır. Bu dosya genellikle projenin kök dizininde bulunur ve hassas bilgilerin (örneğin API anahtarları) kod içinde direkt olarak yazılmasını önler.
- `OPENAI_MODEL_ID` ortam değişkeni: OpenAI'ın API'sini kullanırken hangi modelin kullanılacağını belirlemek için kullanılan bir çevre değişkenidir. Kullanıcı, bu değişkeni `.env` dosyasında yapılandırarak farklı OpenAI modelleri arasında geçiş yapabilir.

Örneğin, `.env` dosyasının içeriği aşağıdaki gibi olabilir:
```makefile
OPENAI_MODEL_ID=GPT-4o-mini
```
Bu şekilde, kod içinde OpenAI modelini çağırmak için `OPENAI_MODEL_ID` değişkeni kullanılır ve model değişikliğinde sadece `.env` dosyasını güncellemek yeterli olur.

---

