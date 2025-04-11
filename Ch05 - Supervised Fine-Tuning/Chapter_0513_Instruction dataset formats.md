#Talimat Veri Kümelerinin Depolanması

Talimat veri kümeleri (Instruction datasets), talimatları ve cevapları organize etmek için belirli bir formatta depolanır. Genellikle, veri kümesindeki her bir örnek (sample), anahtarları (`key`) sistem (`system`), talimat (`instruction`), çıktı (`output`) gibi istem türleri (`prompt types`) ve gerçek metne karşılık gelen değerler (`value`) olan bir Python sözlüğü (`dictionary`) olarak temsil edilebilir. En standart üç format Alpaca, ShareGPT ve OpenAI'dir. Aşağıdaki tablo, bu veri formatlarının genel olarak nasıl organize edildiğini gösterir.

| İsim (`Name`) | JSONL Format (`JSONL format`) |
| --- | --- |
| Alpaca | {“instruction”: “...”, “input”: “...”, “output”: “...”} veya {“instruction”: “...”, “output”: “...”} |
| ShareGPT | {“conversations”: [{“from”: “...”, “value”: “...”}, …]} |
| OpenAI | {“conversations”: [{“role”: “...”, “content”: “...”}, …]} |
| OASST | {“INSTRUCTION”: “...”, “RESPONSE”: “...”} |
| Ham Metin (`Raw text`) | {“text”: “...”} |

Tablo 5.5 – Talimat veri depolama formatı örnekleri

Alpaca için “input” anahtarının (`key`) isteğe bağlı (`optional`) olduğuna dikkat edin. “input” anahtarının içeriği, yalnızca var olduğunda “instruction” anahtarının (`key`) içeriğine eklenir (`appended`). SFT'nin (`SFT`) doğası gereği ön eğitimden (`pre-training`) farklı olmadığını göstermek için “ham metin” (`raw text`) veri formatını da ekledik. Bir modeli ham metin üzerinde yeniden eğitmeye (`re-train`) karar verirseniz, bu genellikle “sürekli ön eğitim” (`continual pre-training`) olarak adlandırılan bir tür ince ayardır (`fine-tuning`).

Önceki bölümde oluşturduğumuz veri kümesi (`dataset`), iki sütuna (`column`) (“instruction” ve “output”) sahiptir ve Alpaca formatına karşılık gelir. Alpaca, tek dönüşlü (`single-turn`) talimatlar ve cevaplar için yeterlidir, yani bir talimat ve bir cevaba sınırlıdır. Sohbetleri (`conversation`) işleme (`process`) istediğinizde (birden fazla talimat ve cevap), ShareGPT veya OpenAI gibi formatlar daha iyi bir seçimdir. Her mesajı (`message`) bir liste (`list`) içinde bir sözlük (`dictionary`) olarak depolayarak, her bir örnekte (`sample`) keyfi uzunlukta (`arbitrarily long`) bir sohbeti temsil edebilirler (`represent`). Tek dönüşlü (`single-turn`) ve çok dönüşlü (`multi-turn`) sohbetler arasındaki seçim (`choice`), depolama türünü (`storage type`) doğrudan etkiler (`impact`) ve son kullanım durumuna (`end use case`) bağlıdır.

**Kodların Ayrıntılı Açıklaması**

1. `{“instruction”: “...”, “input”: “...”, “output”: “...”}` 
   - Bu, Alpaca formatındaki bir veri örneğidir (`data sample`). 
   - “instruction” anahtarı (`key`), talimat metnini (`instruction text`) içerir.
   - “input” anahtarı (`key`), isteğe bağlıdır (`optional`) ve var olduğunda “instruction” metnine eklenir (`appended`).
   - “output” anahtarı (`key`), talimatın (`instruction`) cevabını (`output`) içerir.

2. `{“conversations”: [{“from”: “...”, “value”: “...”}, …]}`
   - Bu, ShareGPT formatındaki bir veri örneğidir (`data sample`).
   - “conversations” anahtarı (`key`), sohbet mesajlarını (`conversation messages`) içeren bir liste (`list`) saklar.
   - Her mesaj (`message`), “from” ve “value” anahtarlarını (`key`) içeren bir sözlük (`dictionary`) olarak temsil edilir (`represented`).

3. `{“conversations”: [{“role”: “...”, “content”: “...”}, …]}`
   - Bu, OpenAI formatındaki bir veri örneğidir (`data sample`).
   - “conversations” anahtarı (`key`), sohbet mesajlarını (`conversation messages`) içeren bir liste (`list`) saklar.
   - Her mesaj (`message`), “role” ve “content” anahtarlarını (`key`) içeren bir sözlük (`dictionary`) olarak temsil edilir (`represented`).

4. `{“INSTRUCTION”: “...”, “RESPONSE”: “...”}`
   - Bu, OASST formatındaki bir veri örneğidir (`data sample`).
   - “INSTRUCTION” anahtarı (`key`), talimat metnini (`instruction text`) içerir.
   - “RESPONSE” anahtarı (`key`), talimatın (`instruction`) cevabını (`response`) içerir.

5. `{“text”: “...”}`
   - Bu, ham metin (`raw text`) formatındaki bir veri örneğidir (`data sample`).
   - “text” anahtarı (`key`), ham metni (`raw text`) içerir.

---

