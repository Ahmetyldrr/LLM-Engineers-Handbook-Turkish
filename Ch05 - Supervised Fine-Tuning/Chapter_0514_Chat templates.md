#Chat Şablonları (Chat Templates)

Talimat-cevap çiftleri (instruction-answer pairs) veri kümesi (dataset) formatından ayrıştırıldıktan sonra, bunları bir sohbet şablonunda (chat template) yapılandırmak istiyoruz. Sohbet şablonları, talimatları ve cevapları modele sunmak için birleşik bir yol sunar. Genel olarak, bir mesajın başlangıcını ve sonunu veya mesajın yazarını tanımlamak için özel belirteçler (special tokens) de içerirler. Temel modeller (base models) talimatları takip etmek üzere tasarlanmadığından, bir sohbet şablonları yoktur. Bu, bir temel modeli ince ayar yaptığınızda herhangi bir şablonu seçebileceğiniz anlamına gelir. Bir talimat modelini (instruct model) ince ayar yapmak istiyorsanız (önerilmez), aynı şablonu kullanmanız gerekir, aksi takdirde performansı düşürebilirsiniz. Talimat veri kümesi formatları gibi, farklı sohbet şablonları vardır: ChatML, Llama 3, Mistral ve diğerleri. Açık kaynak topluluğunda, ChatML şablonu (orijinal olarak OpenAI'den) popüler bir seçenektir. Basitçe iki özel belirteç (<|im_start|> ve <|im_end|>) ekler kim konuşuyor belirlemek için. Size bir örnek vermek gerekirse, Tablo 5.1'de gösterilen talimat-cevap çiftine ChatML şablonunu uyguladığımızda elde ettiğimiz şey şudur: 
```
<|im_start|>system
You are a helpful assistant, who always provide explanation. Think like you are answering to a five year old.<|im_end|>
<|im_start|>user
Concepts: building, shop, town
Write a sentence that includes all these words.<|im_end|>
<|im_start|>assistant
In our little town, there is a shop inside a big building where people go to buy their favorite toys and candies.<|im_end|>
```
Tablo 5.6 – ChatML sohbet şablonu ile Tablo 5.1'den örnek

Gördüğünüz gibi, hala üç farklı bölümümüz var: system, user ve assistant. Her bölüm <|im_start|> belirteci ile başlar ve <|im_end|> ile biter. Mevcut konuşmacı, özel bir belirteç yerine bir dize (örneğin "system") ile tanımlanır. Bu, ince ayar sırasında model tarafından girdi olarak kullanılan ve tokenleştirilen (tokenized) tam dizedir. Ancak, çıkarım (inference) sırasında beklenen cevabı veremeyiz. Bu durumda, Şekil 5.6'da gösterildiği gibi system ve user bölümünü sağlar ve `<|im_start|>assistant\n` ekleyerek modele cevap vermesini isteriz. Model bu şablon ile ince ayar yapıldığından, bir sonraki belirteçlerin (tokens) kullanıcı talimatına uygun ve system istemi (prompt) tarafından yönlendirilen bir cevap olması gerektiğini anlar. İnce ayarlı modellerin talimatları takip etme yetenekleri kazanması böyle gerçekleşir.

Sohbet şablonlarıyla ilgili yaygın bir sorun, her bir boşluk ve satır sonunun (line break) son derece önemli olmasıdır. Herhangi bir karakterin eklenmesi veya kaldırılması yanlış tokenleştirme (tokenization) ile sonuçlanır, bu da modelin performansını olumsuz etkiler. Bu nedenle, Transformers kütüphanesinde uygulandığı gibi Jinja gibi güvenilir şablonların kullanılması önerilir. Tablo 5.7, Alpaca dahil olmak üzere bu tür şablonların birkaç örneğini gösterir; Alpaca hem bir talimat veri kümesi formatının hem de bir sohbet şablonunun adıdır.

#Örnek Sohbet Şablonları

| Ad | Jinja Şablonu |
| --- | --- |
| Alpaca | `### Instruction: What is the capital of France?### Response: The capital of France is Paris.<EOS>` |
| ChatML | `<|im_start|>userWhat is the capital of France?<|im_end|><|im_start|>assistantThe capital of France is Paris.<|im_end|>` |
| Llama 3 | `<|start_header_id|>user<|end_header_id|>What is the capital of France?<|eot_id|><|start_header_id|>assistant<|end_header_id|>The capital of France is Paris.<|eot_id|>` |
| Phi-3 | `<|user|>What is the capital of France?<|end|><|assistant|>The capital of France is Paris.<|end|>` |
| Gemma | `<bos><start_of_turn>userWhat is the capital of France?<end_of_turn><start_of_turn>modelThe capital of France is Paris.<end_of_turn>` |

Tablo 5.7 – Ortak sohbet şablonları örnekleri

Jinja, aynı şablonun hem eğitim hem de çıkarım için kullanılmasına olanak tanıyan döngüler (loops) ve koşullar (conditions) uygular (add_generation_prompt).

### Kodların Ayrıntılı Açıklaması

#### ChatML Şablonu
```
<|im_start|>system
You are a helpful assistant, who always provide explanation. Think like you are answering to a five year old.<|im_end|>
<|im_start|>user
Concepts: building, shop, town
Write a sentence that includes all these words.<|im_end|>
<|im_start|>assistant
In our little town, there is a shop inside a big building where people go to buy their favorite toys and candies.<|im_end|>
```
* `<|im_start|>`: Konuşmacıyı tanımlayan özel bir belirteçtir.
* `system`, `user`, `assistant`: Konuşmacının kimliğini belirten dizelerdir.
* `<|im_end|>`: Konuşmanın sonunu belirten özel bir belirteçtir.

#### Jinja Şablonları
Jinja şablonları, Transformers kütüphanesinde kullanılan şablon motorudur. 
Örneğin, Alpaca şablonu:
```
### Instruction: What is the capital of France?
### Response: The capital of France is Paris.<EOS>
```
* `### Instruction:` ve `### Response:`: Talimat ve cevabı ayıran başlıklardır.
* `<EOS>`: Cümle sonunu belirten özel bir belirteçtir.

Diğer şablonlar da benzer şekilde çalışır, ancak farklı belirteçler ve formatlar kullanabilirler.

---

