#Before exploring each crawler’s implementation, we must present their base class, which defines a unified interface for all the crawlers.

Her bir tarayıcının (crawler) implementasyonunu incelemeden önce, tüm tarayıcılar için birleşik bir arayüz (interface) tanımlayan temel sınıfını (base class) sunmalıyız.

#As shown in Figure 3.4 , we can implement the dispatcher layer because each crawler follows the same signature.

Şekil 3.4'te gösterildiği gibi, her bir tarayıcı aynı imzayı (signature) takip ettiğinden dispatcher katmanını (dispatcher layer) uygulayabiliriz.

#Each class implements the extract() method, allowing us to leverage OOP techniques such as polymorphism, where we can work with abstract objects without knowing their concrete subclass.

Her sınıf, extract() metodunu implemente eder, bu da polimorfizm (polymorphism) gibi Nesne Yönelimli Programlama (OOP) tekniklerinden yararlanmamızı sağlar, burada somut alt sınıfını (concrete subclass) bilmeden soyut nesnelerle (abstract objects) çalışabiliriz.

#For example, in the _crawl_link() function from the ZenML steps, we had the following code: 
crawler = dispatcher.get_crawler(link)
crawler.extract(link=link, user=user)

Örneğin, ZenML adımlarındaki (ZenML steps) _crawl_link() fonksiyonunda, aşağıdaki kod vardı: 
```python
crawler = dispatcher.get_crawler(link)  # dispatcher'dan bir tarayıcı (crawler) alır
crawler.extract(link=link, user=user)  # extract() metodunu çağırır
```
#Note how we called the extract() method without caring about what specific type of crawler we instantiated.

Örnekte görüldüğü gibi, instantiate ettiğimiz tarayıcının (crawler) spesifik tipini umursamadan extract() metodunu çağırdık.

#To conclude, working with abstract interfaces ensures core reusability and ease of extension.

Sonuç olarak, soyut arayüzlerle (abstract interfaces) çalışmak, temel yeniden kullanılabilirliği (core reusability) ve genişletme kolaylığını sağlar.

Kod açıklamaları:
1. `crawler = dispatcher.get_crawler(link)`: 
   - Bu satır, dispatcher nesnesinin (dispatcher object) get_crawler() metodunu çağırarak, verilen link için bir tarayıcı (crawler) nesnesi alır.

2. `crawler.extract(link=link, user=user)`: 
   - Bu satır, alınan tarayıcı nesnesinin (crawler object) extract() metodunu çağırarak, verilen link ve kullanıcı (user) ile işlem yapar.

Bu kodlar, dispatcher katmanının (dispatcher layer) ve tarayıcıların (crawlers) nasıl kullanılacağını gösterir. Dispatcher, farklı tarayıcı tiplerini soyutlayarak (abstracting), istemci kodunun (client code) somut tarayıcı tiplerini bilmeden çalışmasına olanak tanır.

---

