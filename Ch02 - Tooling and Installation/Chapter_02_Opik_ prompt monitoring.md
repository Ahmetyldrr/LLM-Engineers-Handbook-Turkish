# Prompt Loglama ve İzleme

    Standart araçları ve teknikleri prompt loglama ve izleme yaparken kullanamazsınız. Bunun nedeni karmaşıktır. Bunu 11. Bölümde detaylı olarak inceleyeceğiz. Ancak size hızlı bir şekilde anlayış kazandırmak için, standart loglama araçlarını kullanamazsınız çünkü promptlar karmaşık ve yapılandırılmamış zincirlerdir (chains). Bir LLM uygulaması ile etkileşim kurarken, birden fazla girdi promptunu ve oluşturulan çıktıyı bir izleme (trace) içine zincirlersiniz, burada bir prompt önceki promptlara bağlıdır. Bu nedenle, düz metin logları yerine, bu izleri (traces) daha kolay hata ayıklama (debugging) ve izleme (monitoring) için özel bir kontrol paneline (dashboard) gruplandırmanın sezgisel bir yoluna ihtiyacınız vardır. Comet tarafından yapılan açık kaynaklı bir araç olan Opik'i prompt izleme aracı olarak kullandık çünkü Comet'in basitlik ve kullanım kolaylığı felsefesini takip ediyor, ki bu şu anda LLM manzarasında nispeten nadirdir. Benzer özellikler sunan diğer seçenekler Langfuse (açık kaynak, https://langfuse.com), Galileo (açık kaynak değil, rungalileo.io) ve LangSmith (açık kaynak değil, https://www.langchain.com/langsmith) dir, ancak çözümlerini daha hantal bulduk. Opik, sunucusuz (serverless) seçeneğiyle birlikte, tam kontrol sahibi olduğunuz ücretsiz bir açık kaynak sürümü de sağlar. Opik hakkında daha fazla bilgiyi https://github.com/comet-ml/opik adresinden okuyabilirsiniz.

İngilizce Teknik Terimlerle Türkçenin Yanında:
    # Prompt Loglama ve İzleme (Prompt Logging and Monitoring)

    Standart araçları ve teknikleri prompt loglama ve izleme yaparken kullanamazsınız (You cannot use standard tools and techniques when logging and monitoring prompts). Bunun nedeni karmaşıktır (The reason for this is complicated). Bunu 11. Bölümde (Chapter 11) detaylı olarak inceleyeceğiz (We will dig into it). Ancak size hızlı bir şekilde anlayış kazandırmak için, standart loglama araçlarını (standard logging tools) kullanamazsınız çünkü promptlar karmaşık (complex) ve yapılandırılmamış zincirlerdir (unstructured chains). Bir LLM uygulaması (LLM application) ile etkileşim kurarken (interacting with), birden fazla girdi promptunu (input prompts) ve oluşturulan çıktıyı (generated output) bir izleme (trace) içine zincirlersiniz (chain), burada bir prompt önceki promptlara bağlıdır (depends on previous prompts). Bu nedenle, düz metin logları (plain text logs) yerine, bu izleri (traces) daha kolay hata ayıklama (debugging) ve izleme (monitoring) için özel bir kontrol paneline (specialized dashboard) gruplandırmanın sezgisel bir yoluna (intuitive way) ihtiyacınız vardır. Comet tarafından yapılan açık kaynaklı bir araç olan Opik'i (Opik, an open-source tool made by Comet) prompt izleme aracı (prompt monitoring tool) olarak kullandık çünkü Comet'in basitlik (simplicity) ve kullanım kolaylığı (ease of use) felsefesini takip ediyor, ki bu şu anda LLM manzarasında (LLM landscape) nispeten nadirdir. Benzer özellikler sunan diğer seçenekler Langfuse (açık kaynak, https://langfuse.com), Galileo (açık kaynak değil, rungalileo.io) ve LangSmith (açık kaynak değil, https://www.langchain.com/langsmith) dir, ancak çözümlerini daha hantal (cumbersome) bulduk. Opik, sunucusuz (serverless) seçeneğiyle birlikte, tam kontrol sahibi olduğunuz (complete control over) ücretsiz bir açık kaynak sürümü (free open-source version) de sağlar. Opik hakkında daha fazla bilgiyi https://github.com/comet-ml/opik adresinden okuyabilirsiniz.

Kodların Ayrıntılı Açıklaması:
    Bu metinde kod bulunmamaktadır. 

Ancak aşağıdaki kısım detaylı olarak açıklanabilir:
```
# Prompt Loglama ve İzleme
```
- Bu, bir başlıktır ve Prompt Loglama ve İzleme konusuna işaret eder.

Diğer kısımlar İngilizce teknik terimlerle birlikte Türkçeye çevrilmiştir ve her bir cümle detaylı olarak açıklanmıştır.

Örneğin:
```
Standart araçları ve teknikleri prompt loglama ve izleme yaparken kullanamazsınız (You cannot use standard tools and techniques when logging and monitoring prompts).
```
- Bu cümle, standart araçların ve tekniklerin prompt loglama ve izlemede kullanılamayacağını belirtir.

Benzer şekilde, diğer cümleler de aynı şekilde açıklanabilir.

---

