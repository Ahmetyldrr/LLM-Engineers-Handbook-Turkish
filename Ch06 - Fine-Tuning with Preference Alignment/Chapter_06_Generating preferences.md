#Tercih Veri Kümeleri (Preference Datasets) Oluşturma

Yeni tercih veri kümeleri oluşturmadan önce, ilgili açık kaynaklı veri kümelerini incelemek iyi bir fikirdir. Talimat veri kümelerine kıyasla daha az sayıda tercih veri kümesi vardır, ancak Hugging Face Hub'da yüksek kaliteli tercih veri kümeleri bulabilirsiniz. Bunlar belirli görevler için veya kendi veri kümenize eklemek için kullanılabilir. İyi bilinen tercih veri kümeleri arasında, yardımcı ve zararsız AI yanıtları için insan tercihleri içeren Anthropic HH-RLHF veri kümesi ve makale özetlerine odaklanan OpenAI Summarize from Human Feedback veri kümesi bulunur.

DPO (Direct Preference Optimization) veri kümeleri çeşitli yöntemler kullanılarak oluşturulabilir, her birinin kalite, maliyet ve ölçeklenebilirlik arasında kendi trade-off'ları vardır. Bu yöntemler belirli uygulamalara uyarlanabilir ve farklı derecelerde insan geri bildirimi gerektirir. Bunları dört ana kategoriye ayırıyoruz:

##İnsan Üretimi, İnsan Değerlendirmeli Veri Kümeleri (Human-generated, human-evaluated datasets)
Bu yöntem, insanları hem istemlere yanıtlar oluşturmak hem de bu yanıtların kalitesini değerlendirmek için işe almak anlamına gelir. Bu yaklaşım, nüanslı insan tercihlerini yakalayabilir ve karmaşık görevler için idealdir, ancak son derece kaynak yoğun ve ölçeklendirilmesi zordur. Sonuç olarak, öncelikle büyük AI şirketleri tarafından kullanılır.

```markdown
### İnsan Üretimi, İnsan Değerlendirmeli Veri Kümeleri
- İnsanlar tarafından hem yanıt oluşturma hem de değerlendirme yapılır.
- Nüanslı insan tercihlerini yakalar.
- Kaynak yoğun ve ölçeklendirilmesi zordur.
```

##İnsan Üretimi, LLM Değerlendirmeli Veri Kümeleri (Human-generated, LLM-evaluated datasets)
Bu yöntem, çok sayıda mevcut insan tarafından oluşturulan içerik varsa yararlı olabilir. Ancak, pratikte verimsizlik nedeniyle nadiren kullanılır, çünkü hala yanıt oluşturma için önemli ölçüde insan girdisi gerektirirken, LLM değerlendirme aşamasında nüanslı tercihleri kaçırabilir.

```markdown
### İnsan Üretimi, LLM Değericillermeli Veri Kümeleri
- Mevcut insan tarafından oluşturulan içerik için yararlı olabilir.
- Verimsiz ve insan girdisi gerektirir.
```

##LLM Üretimi, İnsan Değerlendirmeli Veri Kümeleri (LLM-generated, human-evaluated datasets)
Bu yöntem, kalite ve verimlilik arasında iyi bir denge sunar. LLM'ler istemlere birden fazla yanıt üretir ve insanlar bu yanıtları sıralar. Bu yaklaşım, insanların sıfırdan yazmaktan daha iyi değerlendirme yapabildikleri için genellikle tercih edilir. Çeşitli yanıtların hızlı bir şekilde oluşturulmasına izin verirken, insan tercihlerini etkili bir şekilde yakalar. Ancak, insanların üretebileceği yaratıcı veya beklenmedik yanıtları sağlamayabilir.

```markdown
### LLM Üretimi, İnsan Değerlendirmeli Veri Kümeleri
- Kalite ve verimlilik arasında iyi bir denge sunar.
- İnsanlar tarafından yanıtların sıralanması yapılır.
- Çeşitli ve hızlı yanıtlar sağlar.
```

##LLM Üretimi, LLM Değerlendirmeli Veri Kümeleri (LLM-generated, LLM-evaluated datasets)
Tamamen sentetik veri kümeleri, hem oluşturma hem de değerlendirme LLM'ler tarafından yapıldığında, ölçeklenebilirlik ve maliyet etkinliği nedeniyle giderek daha yaygın hale gelmektedir. Bu yöntem, LLM yetenekleri geliştikçe hızla büyük veri kümeleri üretebilir. Ancak, kalite ve çeşitliliği sağlamak için dikkatli istem mühendisliği gerektirir ve oluşturan LLM'nin önyargılarını veya sınırlamalarını sürdürebilir.

```markdown
### LLM Üretimi, LLM Değerlendirmeli Veri Kümeleri
- Ölçeklenebilir ve maliyet etkili.
- Hızlı bir şekilde büyük veri kümeleri üretir.
- Dikkatli istem mühendisliği gerektirir.
```

Pratikte, insan tarafından oluşturulan veri kümeleri pahalı, ölçeklendirilmesi zor ve her zaman en yüksek kalitede olmayabilir. Öte yandan, insan değerlendirmesi oldukça değerlidir ancak ölçeklendirilmesi zor olabilir, bu nedenle büyük veri kümeleri LLM değerlendirmesinden yararlanır. Bu yüksek seviyeli değerlendirmelere ek olarak, verilerinizi nasıl elde ettiğiniz ve nasıl kullanmayı planladığınız da dikkate alınmalıdır. Örneğin, birçok kullanıcısı olan uygulamalar, tercih sağlamak için bir geri bildirim mekanizması gömebilir. Bu, basit bir beğenme ve beğenmeme puanı veya metinle daha derinlemesine bir şey olabilir. Değerlendirmenin her zaman gerekli olmadığını ve tercihlerin doğal olarak oluşturma sürecinden ortaya çıkabileceğini unutmayın. Örneğin, tercih edilen çıktıları oluşturmak için yüksek kaliteli bir model ve daha az tercih edilen alternatifler üretmek için daha düşük kaliteli veya kasıtlı olarak hatalı bir model kullanmak mümkündür. Bu, tercih veri kümesinde net bir ayrım yaratır ve AI sistemlerinin yüksek kaliteli çıktıları tanımak ve taklit etmek için daha etkili bir şekilde eğitilmesini sağlar. Hugging Face Hub'da bulunan Intel/orca_dpo_pairs veri kümesi bu süreçle oluşturulmuştur. Başka bir yaklaşım, model tarafından oluşturulan çıktıları insan tarafından yazılmış yanıtlarla karşılaştırmaktır, bu da modelin gerçek insan tercihleriyle ne kadar uyumlu olduğu konusunda içgörüler sağlayabilir ve modelin eksik olabileceği alanları vurgulayabilir. Bu, belirli bir stili kopyalamak ve modele daha otantik bir ton vermek için kullanılabilir.

```markdown
### Önemli Noktalar
- İnsan tarafından oluşturulan veri kümeleri pahalı ve ölçeklendirilmesi zordur.
- İnsan değerlendirmesi değerlidir ancak ölçeklendirilmesi zor olabilir.
- LLM değerlendirmesi büyük veri kümeleri için yararlıdır.
- Veri elde etme ve kullanma yöntemleri önemlidir.
- Geri bildirim mekanizmaları tercih sağlamak için kullanılabilir.
- Tercihler doğal olarak oluşturma sürecinden ortaya çıkabilir.
```

---

