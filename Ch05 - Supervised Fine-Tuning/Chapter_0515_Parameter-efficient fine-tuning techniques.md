#SFT Teknikleri: Tam İnce Ayar (Full Fine-Tuning), LoRA ve QLoRA

Literatürde birçok teknik mevcut olmakla birlikte, SFT üç ana teknik üzerinde yoğunlaşmıştır: tam ince ayar (full fine-tuning), LoRA ve QLoRA. Her bir tekniği bireysel olarak tanıtacağız ve kullanım durumlarınıza (use cases) bağlı olarak avantajlarını ve dezavantajlarını tartacağız. 

Şekil 5.9 – Üç ana SFT tekniğinin modül seviyesindeki (module level) mimari farklılıkları

İngilizce Teknik Terimler:
- SFT: Supervised Fine-Tuning (Gözetimli İnce Ayar)
- LoRA: Low-Rank Adaptation (Düşük-Ranklı Uyarlama)
- QLoRA: Quantized Low-Rank Adaptation (Nicemlenmiş Düşük-Ranklı Uyarlama)

Kod bulunmamaktadır, sadece bir paragraf çevirisi ve açıklamasını yaptık.

Eğer kod içermeyen bir metin çevirisi istenirse, yukarıdaki gibi bir çeviri yapılır. 

Ancak eğer kod örneği verilseydi, kodun her bir satırını açıklayarak Türkçeye çevirirdik. Örneğin aşağıdaki gibi bir kod için:

```python
def greet(name: str) -> None:
    print(f"Hello, {name}!")
```

Çeviri ve açıklama aşağıdaki gibi olurdu:

```python
# İsim parametresi alan ve karşılama mesajı yazdıran fonksiyon
def greet(name: str) -> None:
    # name parametresini string formatında karşılama mesajı içine yerleştirip yazdırır
    print(f"Hello, {name}!")
```

Kodun her satırı açıklanarak Türkçeye çevrilmiştir. 

Bu örnekte kod olmadığı için, sadece metin çevirisi yapılmıştır.

---

