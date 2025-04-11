#Tercih Veri Kümeleri Oluşturma (Creating Preference Datasets)

Tercih veri kümeleri (preference datasets) oluştururken, veri üretimi (data generation) ve değerlendirme (evaluation) yakından ilişkilidir. Öncelikle cevaplar (answers) oluştururuz ve ardından nihai veri kümesini (final dataset) oluşturmak için onları derecelendiririz (rate them). Aşağıda, bu iki adımı ayrı ayrı değil, bir süreç olarak tanıtıyoruz.

İngilizce paragraf:
`When creating preference datasets, data generation and evaluation are closely linked. We first create answers and then rate them to make the final dataset. In the following, we introduce both steps as one process instead of two separate ones.`

Türkçe Çeviri:
`Tercih veri kümeleri oluştururken, veri üretimi ve değerlendirme yakından ilişkilidir. Öncelikle cevaplar oluştururuz ve ardından nihai veri kümesini oluşturmak için onları derecelendiririz. Aşağıda, bu iki adımı ayrı ayrı değil, bir süreç olarak tanıtıyoruz.`

Kodlama yapılmamıştır, sadece bir paragraf çevirisi yapılmıştır.

Eğer kodlama yapılmış olsaydı, kodların açıklamaları aşağıdaki gibi olacaktı:

Örneğin, yukarıdaki metni işlemek için basit bir Python kodu yazalım:

```python
# Metni tanımla
metin = "When creating preference datasets, data generation and evaluation are closely linked. We first create answers and then rate them to make the final dataset. In the following, we introduce both steps as one process instead of two separate ones."

# Metni Türkçe'ye çevir
ceviri = "Tercih veri kümeleri oluştururken, veri üretimi ve değerlendirme yakından ilişkilidir. Öncelikle cevaplar oluştururuz ve ardından nihai veri kümesini oluşturmak için onları derecelendiririz. Aşağıda, bu iki adımı ayrı ayrı değil, bir süreç olarak tanıtıyoruz."

# İngilizce teknik terimleri parantez içinde ekle
sonuc = "#Tercih Veri Kümeleri Oluşturma (Creating Preference Datasets)\n\n" + çeviri + "\n\nİngilizce paragraf:\n`" + metin + "`\n\nTürkçe Çeviri:\n`" + çeviri + "`"

# Sonucu yazdır
print(sonuc)
```

Kod açıklamaları:

1. `metin = "When creating preference datasets, data generation and evaluation are closely linked. We first create answers and then rate them to make the final dataset. In the following, we introduce both steps as one process instead of two separate ones."` 
   - Bu satırda, işlenecek İngilizce metin tanımlanır.

2. `ceviri = "Tercih veri kümeleri oluştururken, veri üretimi ve değerlendirme yakından ilişkilidir. Öncelikle cevaplar oluştururuz ve ardından nihai veri kümesini oluşturmak için onları derecelendiririz. Aşağıda, bu iki adımı ayrı ayrı değil, bir süreç olarak tanıtıyoruz."` 
   - Bu satırda, İngilizce metnin Türkçe çevirisi tanımlanır.

3. `sonuc = "#Tercih Veri Kümeleri Oluşturma (Creating Preference Datasets)\n\n" + çeviri + "\n\nİngilizce paragraf:\n`" + metin + "`\n\nTürkçe Çeviri:\n`" + çeviri + "`"` 
   - Bu satırda, başlık (#Tercih Veri Kümeleri Oluşturma), Türkçe çeviri, orijinal İngilizce metin ve çeviri birleştirilerek sonuç (`sonuc`) değişkenine atanır.

4. `print(sonuc)` 
   - Bu satırda, oluşturulan `sonuc` değişkeni yazdırılır.

---

