#Toplu İşlem Boyutu ve Gradyan Birikimi
Toplu işlem boyutu (Batch size), modelin ağırlıkları güncellenmeden önce işlenen örnek sayısını belirler. Büyük dil modeli (LLM) ince ayar (fine-tuning) için tipik toplu işlem boyutları 1 ila 32 arasında değişir ve ortak değerler 1, 2, 4, 8 veya 16'dir. Daha büyük toplu işlem boyutları genellikle daha kararlı gradyan (gradient) tahminlerine yol açar ve eğitim hızını artırabilir, çünkü tüm veri setinin gerçek gradyanına daha iyi bir yaklaşım sağlar. Ancak, daha fazla bellek gerektirirler, bu da daha az VRAM (Video Random Access Memory) olan GPU'lar (Graphics Processing Unit) için sınırlayıcı bir faktör olabilir. Örneğin, 24 GB belleğe sahip yüksek kaliteli bir GPU'da 16 toplu işlem boyutu iyi çalışabilirken, 8 GB belleğe sahip daha küçük bir GPU yalnızca 2 veya 4 toplu işlem boyutunu işleyebilir. Bellek kısıtlamalarını aşmak ve hala daha büyük toplu işlem boyutlarından yararlanmak için gradyan birikimi (gradient accumulation) adı verilen bir teknik kullanılabilir. Bu teknik, daha küçük mini toplu işlemlerle (mini-batch) birden fazla ileri ve geri geçiş yaparak, bu adımlar boyunca gradyanları biriktirerek ve ardından modelin parametrelerine tek bir güncelleme uygulayarak çalışır. Bu yaklaşım, büyük modeller veya sınırlı GPU belleği ile çalışırken özellikle yararlıdır. Örneğin, 32 etkili toplu işlem boyutuna ulaşmak istiyorsanız ancak GPU'nuz aynı anda yalnızca 8 örnek işleyebiliyorsa, gradyan birikimi adımlarını 4 olarak ayarlayabilirsiniz. Bu, 8 örnekten oluşan 4 mini toplu işlemi işleyeceğiniz, gradyanları biriktireceğiniz ve ardından modeli, sanki tüm 32 örneği aynı anda işlemış gibi güncelleyeceğiniz anlamına gelir. Gradyan birikimi adımlarının sayısı tipik olarak 1 (birikim yok) ila 8 veya 16 arasında değişir, istenen etkili toplu işlem boyutuna ve mevcut hesaplama kaynaklarına bağlıdır. Adım sayısını seçerken, eğitim hızı ve bellek kullanımı arasındaki dengeyi göz önünde bulundurun. Daha fazla birikim adımı, daha büyük etkili toplu işlem boyutlarına izin verir, ancak her güncelleme için gereken zamanı artırır. Etkili toplu işlem boyutunu belirlemek için basit bir formül vardır: 
Örneğin, her biri 4 örnekten oluşan bir toplu işlemi işleyen 2 GPU kullanıyorsanız, 4 gradyan birikimi adımı ile etkili toplu işlem boyutunuz 4 * 2 * 4 = 32 örnek olacaktır.

```python
#örnek kod yok
# efektif_toplu_islem_boyutu = toplu_islem_boyutu * gpu_sayisi * gradyan_birikimi_adimlari
efektif_toplu_islem_boyutu = 4 * 2 * 4 
print(efektif_toplu_islem_boyutu)  # Çıktı: 32
```

Kod açıklamaları:
- `#örnek kod yok`: Bu satır, örnek kodun olmadığını belirtir.
- `efektif_toplu_islem_boyutu = toplu_islem_boyutu * gpu_sayisi * gradyan_birikimi_adimlari`: Etkili toplu işlem boyutunu hesaplamak için kullanılan formül. 
  - `toplu_islem_boyutu`: Her bir GPU'nun işlediği örnek sayısı.
  - `gpu_sayisi`: Kullanılan GPU sayısı.
  - `gradyan_birikimi_adimlari`: Gradyan birikimi için yapılan adımların sayısı.
- `efektif_toplu_islem_boyutu = 4 * 2 * 4`: Örnek değerler kullanılarak efektif toplu işlem boyutunun hesaplanması.
  - `4`: Her bir GPU'nun işlediği örnek sayısı (`toplu_islem_boyutu`).
  - `2`: Kullanılan GPU sayısı (`gpu_sayisi`).
  - `4`: Gradyan birikimi adımlarının sayısı (`gradyan_birikimi_adimlari`).
- `print(efektif_toplu_islem_boyutu)`: Hesaplanan efektif toplu işlem boyutunun yazdırılması.
  - `Çıktı: 32`: Programın çıktısı, efektif toplu işlem boyutunun 32 olduğunu gösterir.

---

