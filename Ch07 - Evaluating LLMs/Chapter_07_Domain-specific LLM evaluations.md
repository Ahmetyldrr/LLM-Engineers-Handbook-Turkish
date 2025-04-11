# Domain-Specific LLMs Değerlendirmeleri
Domain-specific LLMs (Büyük Dil Modelleri), genel amaçlı modellerle aynı kapsamda değildir. Bu, önceki ölçütlere göre daha derinlemesine daha ince taneli yetenekleri hedeflemeye yardımcı olur. Kategori içinde, ölçüt seçimi tamamen ilgili alanın kendisine bağlıdır. Dil spesifik modeli veya kod modeli gibi yaygın uygulamalar için, ilgili değerlendirmeleri ve hatta ölçüt takımlarını (benchmark suite) aramak önerilir. Bu takımlar, farklı ölçütleri kapsar ve tekrarlanabilir olacak şekilde tasarlanmıştır. Bir alanın farklı yönlerini hedefleyerek, genellikle alan performansını daha doğru bir şekilde yakalarlar. Bunu göstermek için, Hugging Face Hub'daki lider tabloları (leaderboard) ile birlikte alan-specifik değerlendirmelerin bir listesi aşağıda verilmiştir:

## Open Medical-LLM Leaderboard 
Tıbbi soru-cevap görevlerinde LLMs'in performansını değerlendirir. 9 ölçütü yeniden gruplandırır, US tıbbi lisans sınavlarından 1.273 soru (MedQA), PubMed makalelerinden 500 soru (PubMedQA), Hint tıbbi giriş sınavlarından 4.183 soru (MedMCQA) ve MMLU'nun 6 alt kategorisinden 1.089 soru (klinik bilgi, tıbbi genetik, anatomi, profesyonel tıp, üniversite biyolojisi ve üniversite tıbbı) içerir.

## BigCodeBench Leaderboard 
Kod LLMs'in performansını değerlendirir, iki ana kategori içerir: yapılandırılmış doküman dizelerine (docstring) dayalı kod tamamlama için BigCodeBench-Complete ve doğal dil komutlarından kod oluşturma için BigCodeBench-Instruct. Modeller, greedy decoding kullanılarak Pass@1 skorlarına göre sıralanır, Complete varyantı için ek bir Elo derecelendirmesi ile. LLMs'in kompozisyonel muhakeme ve komut takibi yeteneklerini test eden geniş bir programlama senaryoları yelpazesini kapsar.

## Hallucinations Leaderboard 
LLMs'in 5 kategori boyunca 16 farklı görevde yanlış veya desteklenmeyen bilgi üretme eğilimini değerlendirir. Bunlar Soru-Cevap (NQ Open, TruthfulQA ve SQuADv2 gibi veri kümeleriyle), Okuduğunu Anlama (TriviaQA ve RACE kullanarak), Özetleme (HaluEval Summ, XSum ve CNN/DM kullanarak), Diyalog (HaluEval Dial ve FaithDial kullanarak) ve Gerçek Kontrolü (MemoTrap, SelfCheckGPT, FEVER ve TrueFalse kullanarak) içerir. Lider tablosu ayrıca IFEval kullanarak komut takibi yeteneğini değerlendirir.

## Enterprise Scenarios Leaderboard 
LLMs'in altı gerçek dünya işletme kullanım senaryosundaki performansını değerlendirir, iş uygulamalarına ilişkin çeşitli görevleri kapsar. Ölçütler FinanceBench (alınan içerikle 100 finansal soru), Legal Confidentiality (LegalBench'ten 100 istem yasal muhakeme için), Writing Prompts (yaratıcı yazma değerlendirmesi), Customer Support Dialogue (müşteri hizmeti etkileşimlerinde alaka), Toxic Prompts (zararlı içerik oluşturma için güvenlik değerlendirmesi) ve Enterprise PII (duyarlı bilgi koruması için işletme güvenliği) içerir. Bazı test kümeleri lider tablosunun kötüye kullanılmasını önlemek için kapalı-kaynaklıdır. Değerlendirme, cevap doğruluğu, yasal muhakeme, yaratıcı yazma, bağlamsal alaka ve güvenlik önlemleri gibi belirli yeteneklere odaklanır, LLMs'in işletme ortamlarına uygunluğunu kapsamlı bir şekilde değerlendirir.

Lider tablolar, alanlarına bağlı olarak farklı yaklaşımlara sahip olabilir. Örneğin, BigCodeBench, tüm alanı yeterince yakalayan sadece iki ölçüt üzerine kuruludur. Diğer yandan, Hallucinations Leaderboard 16 ölçütü yeniden gruplandırır, birçok genel amaçlı değerlendirmeyi içerir. Özel ölçütlerin yanı sıra genel amaçlı olanların yeniden kullanılmasının kendi ölçüt takımınızı tamamlayabileceğini gösterir. Özellikle, dil-specifik LLMs genellikle genel amaçlı ölçütlerin çevrilmiş sürümlerini yeniden kullanır. Bu, yerel dilde orijinal değerlendirmelerle tamamlanabilir. Bu ölçütlerin bazıları makine çevirisi kullanırken, kalitelerini artırmak için insan çevirisine güvenmek daha iyidir.

Aşağıdaki üç görev-specifik lider tablosunu ve ilgili değerlendirme takımlarını, kendi ölçütlerinizi nasıl oluşturacağınıza dair bir fikir vermek için seçtik:
### OpenKo-LLM Leaderboard 
Korean LLMs'in performansını dokuz ölçüt kullanarak değerlendirir. Bu ölçütler, Koreceye çevrilmiş genel amaçlı ölçütlerin (GPQA, Winogrande, GSM8K, EQ-Bench ve IFEval) ve özel değerlendirmelerin (Knowledge, Social Value, Harmlessness ve Helpfulness) bir kombinasyonudur.

### Open Portuguese LLM Leaderboard 
Portekizce dil LLMs'in performansını dokuz farklı ölçüt kullanarak değerlendirir. Bu ölçütler eğitim değerlendirmeleri (1.430 soru ile ENEM ve üniversite giriş sınavlarından 724 soru ile BLUEX), profesyonel sınavlar (2.000'den fazla soru ile OAB Sınavları), dil anlama görevleri (ASSIN2 RTE ve STS, FAQUAD NLI) ve sosyal medya içerik analizi (7.000 Instagram yorumu ile HateBR, 5.668 tweet ile PT Hate Speech ve tweetSentBR) içerir.

### Open Arabic LLM Leaderboard 
Arapça dil LLMs'in performansını kapsamlı bir ölçüt seti kullanarak değerlendirir, hem yerel Arapça görevleri hem de çevrilmiş veri kümelerini içerir. Lider tablosu iki yerel Arapça ölçütü içerir: AlGhafa ve Arabic-Culture-Value-Alignment. Ayrıca, MMLU, ARC-Challenge, HellaSwag ve PIQA gibi çeşitli alanları kapsayan 12 çevrilmiş ölçütü içerir.

Hem genel amaçlı hem de alana özgü değerlendirmeler üç ana prensip üzerine tasarlanır. İlk olarak, iyi ve kötü çıktıları ayırt etmek için modellere meydan okumalıdır. İkinci olarak, çeşitli olmalıdır ve mümkün olduğunca çok konu ve senaryoyu kapsamalıdır. Bir ölçüt yeterli olmadığında, ek ölçütler daha güçlü bir takım oluşturabilir. Son olarak, pratik ve yürütülmesi kolay olmalıdır. Bu, daha çok veya daha az karmaşık çalışabilen değerlendirme kütüphanelerine bağlıdır. Ölçütlerinizi yürütmek için Eleuther AI'dan lm-evaluation-harness (github.com/EleutherAI/lm-evaluation-harness) ve Hugging Face'den lighteval (github.com/huggingface/lighteval) kullanmanızı öneririz.

Kod Açıklaması:
```markdown
# Yukarıdaki metinde kod bulunmamaktadır, ancak önerilen iki kütüphane vardır:
# 1. lm-evaluation-harness: github.com/EleutherAI/lm-evaluation-harness
# 2. lighteval: github.com/huggingface/lighteval

# Bu kütüphanelerin kullanımı ile ilgili örnek kod aşağıdaki gibidir:

# lm-evaluation-harness için örnek kullanım:
# git clone https://github.com/EleutherAI/lm-evaluation-harness.git
# cd lm-evaluation-harness
# pip install -e .

# lighteval için örnek kullanım:
# git clone https://github.com/huggingface/lighteval.git
# cd lighteval
# pip install -e .

# Her iki kütüphane için de dokümantasyon ve örnek kullanım komutları ilgili GitHub sayfalarında bulunabilir.
```

---

