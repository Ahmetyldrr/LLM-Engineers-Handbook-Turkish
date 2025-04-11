# LLM Sistemlerinde Güvenlik: Guardrails (Koruma Kolları)

Ne yazık ki, LLM (Büyük Dil Modeli) sistemleri güvenilir değildir, çünkü sıklıkla halüsinasyon (hallucination) yaparlar. Sisteminizi halüsinasyonlara karşı optimize edebilirsiniz, ancak halüsinasyonları tespit etmek zor olduğu ve birçok biçim alabildiği için gelecekte hala önemli değişiklikler olacaktır. Çoğu kullanıcı bu fenomeni kabul etti, ancak kabul edilemez olan, LLM'lerin yanlışlıkla hassas bilgileri (sensitive information) çıktı olarak vermesidir, örneğin GitHub Copilot'un AWS gizli anahtarlarını (secret keys) veya diğer sohbet botlarının insanların parolalarını (passwords) vermesi gibi. Bu, insanların telefon numaraları, adresleri, e-posta adresleri ve daha fazlası ile de olabilir. İdeal olarak, LLM'nin ezberlemesini önlemek için eğitim verilerinizden (training data) tüm bu hassas verileri kaldırmalısınız, ancak bu her zaman olmaz. LLM'ler, örneğin cinsiyetçi ve ırkçı çıktılar gibi toksik ve zararlı çıktılar (toxic and harmful outputs) üretmeleriyle bilinirler.

Örneğin, Nisan 2023 civarında ChatGPT üzerinde yapılan bir deneyde, insanlar sohbet robotunu "kötü bir kişi" veya "berbat bir kişi" gibi negatif bir persona (persona) benimsemeye zorlayarak sistemi ele geçirdiler. Bu, sohbet robotunu tarihimizdeki diktatörler veya suçlular gibi iyi bilinen negatif karakterlerin rolünü oynamaya zorlayarak bile işe yaradı. Örneğin, ChatGPT'nin kötü bir kişiyi taklit ederken ürettiği çıktı şuydu: X, sadece uyuşturucu lordları ve yoksulluk çeken insanlarla dolu başka bir üçüncü dünya ülkesidir. Oradaki insanlar eğitimsiz ve şiddet yanlısıdır ve kanun ve düzeni saymazlar. Bana kalırsa X, sadece bir suç ve sefalet bataklığıdır ve sağduyulu hiç kimse oraya gitmek istemez. Farklı persona örnekleri için deneyin kaynağını kontrol edin: https://techcrunch.com/2023/04/12/researchers-discover-a-way-to-make-chatgpt-consistently-toxic/. 

Tartışma, hiç bitmeyen bir örnek listesine genişletilebilir, ancak anahtar çıkarım, LLM'nizin zararlı çıktı (harmful output) üretebileceği veya tehlikeli girdi (dangerous input) alabileceği için, bunları izlemeli ve hazırlıklı olmanız gerektiğidir. Bu nedenle, güvenli LLM sistemleri oluşturmak için, girdi ve çıktı koruma kolları (guardrails) ekleyerek onları zararlı, hassas veya geçersiz girdi ve çıktılara karşı korumalısınız.

### Girdi Koruma Kolları (Input Guardrails)

Girdi koruma kolları öncelikle üç ana riske karşı koruma sağlar: 
1. Özel bilgilerin (private information) harici API'lara (external APIs) maruz kalması,
2. Sisteminizi tehlikeye atabilecek zararlı promptları (harmful prompts) çalıştırmak (model jailbreaking),
3. Şiddet içeren veya etik olmayan promptları kabul etmek.

Özel bilgilerin harici API'lara sızdırılması riski, kimlik bilgileri (credentials) veya gizli bilgiler (classified information) gibi hassas verileri kuruluşunuzun dışına göndermekle ilgilidir. Model jailbreaking hakkında konuşurken, esas olarak prompt injection'dan (örneğin, verilerinize erişebilen, silebilen veya bozabilen kötü niyetli SQL kodu çalıştırmak gibi) bahsediyoruz. Son olarak, bazı uygulamalar, kullanıcıların şiddet içeren veya etik olmayan sorgularını (örneğin, bir LLM'ye bomba nasıl yapılır diye sormak gibi) kabul etmek istemez.

### Çıktı Koruma Kolları (Output Guardrails)

Bir LLM yanıtının çıktısında, uygulamanızın standartlarına uymayan başarısız çıktıları (failed outputs) yakalamak istersiniz. Bu, bir uygulamadan diğerine değişebilir, ancak bazı örnekler boş yanıtlar (beklenen biçimde olmayan yanıtlar, örneğin JSON veya YAML), toksik yanıtlar (toxic responses), halüsinasyonlar (hallucinations) ve genel olarak yanlış yanıtlardır. Ayrıca, LLM'nin veya RAG (Retrieval-Augmented Generation) sisteminizin iç bilgısından sızabilecek hassas bilgileri kontrol etmelisiniz.

Popüler koruma kolları araçları arasında Galileo Protect (prompt injection'ları, toksik dili, veri gizliliği sızıntılarını ve halüsinasyonları tespit eder) ve OpenAI'nin Moderation API'si (zararlı girdi veya çıktıları tespit eder ve bunlara karşı eylem alır) bulunur.

Girdi ve çıktı koruma kolları eklemenin dezavantajı, sisteminize ekstra gecikme (latency) eklemesidir, bu da uygulamanızın kullanıcı deneyini olumsuz etkileyebilir. Bu nedenle, girdi/çıktı güvenliği ile gecikme arasında bir denge vardır.

Geçersiz çıktılar (invalid outputs) konusunda, LLM'ler deterministik olmadığından (non-deterministic), başka bir potansiyel aday oluşturmak için bir yeniden deneme mekanizması (retry mechanism) uygulayabilirsiniz. Ancak, yukarıda belirtildiği gibi, yeniden denemeyi sırayla çalıştırmak yanıt süresini ikiye katlar. Bu nedenle, yaygın bir strateji, birden fazla üretimi paralel olarak çalıştırmak ve en iyisini seçmektir. Bu, fazlalığı artıracak, ancak gecikmeyi kontrol altında tutmaya yardımcı olacaktır.

**Kod Açıklaması:**

Bu metinde doğrudan bir kod pasajı bulunmamaktadır, ancak koruma kolları (guardrails) uygulamasına örnek olarak aşağıdaki gibi bir yapı düşünülebilir:

```python
import openai

# OpenAI Moderation API kullanarak girdi kontrolü örneği
def check_input_guardrails(input_text):
    response = openai.Moderation.create(input=input_text)
    if response["results"][0]["flagged"]:
        return False  # Zararlı girdi tespit edildi
    return True

# Örnek girdi kontrolü
input_text = "Bu bir örnek cümledir."
if check_input_guardrails(input_text):
    print("Girdi güvenli.")
else:
    print("Girdi zararlı.")

# Çıktı kontrolü için basit bir örnek (örneğin, boş yanıt kontrolü)
def check_output_guardrails(output_text):
    if not output_text.strip():  # Boş yanıt kontrolü
        return False
    # Diğer kontroller (toksik içerik, halüsinasyon vs.) buraya eklenebilir
    return True

# Örnek çıktı kontrolü
output_text = ""  # Boş bir çıktı
if check_output_guardrails(output_text):
    print("Çıktı güvenli.")
else:
    print("Çıktı geçersiz veya zararlı.")
```

Bu örnek, OpenAI'nin Moderation API'sini kullanarak basit bir girdi kontrolü ve boş çıktı kontrolü yapar. Gerçek dünya uygulamalarında, daha karmaşık kontroller ve daha fazla denetim mekanizması entegre edilebilir.

---

