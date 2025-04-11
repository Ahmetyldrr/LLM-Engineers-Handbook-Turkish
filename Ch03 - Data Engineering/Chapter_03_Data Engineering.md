#Bu Bölümde Neler Öğreneceğiz?
Bu bölüm, LLM Twin projesini daha derinlemesine incelemeye başlayacaktır. İnce ayar (fine-tuning) veya çıkarım (inference) gibi tüm LLM kullanım senaryolarımızda kullanacağımız ham verileri toplamak için veri toplama hattını nasıl tasarlayacağımızı ve uygulayacağımızı öğreneceğiz. Bu bir veri mühendisliği kitabı olmadığı için, bu bölümü kısa tutacağız ve yalnızca gerekli ham verileri toplamak için kesinlikle gerekli olan şeylere odaklanacağız. 4. Bölüm'den itibaren, LLM'ler (LLM) ve GenAI (GenAI) üzerine yoğunlaşacağız, teorisini ve somut uygulama detaylarını inceleyeceğiz. Oyuncak projeler (toy projects) üzerinde çalışırken veya araştırma yaparken, genellikle üzerinde çalıştığınız statik bir veri kümesi (static dataset) olur. Ancak LLM Twin kullanım senaryomuzda, gerçek dünya senaryosunu taklit etmek istiyoruz, bu nedenle verileri kendimiz toplamak ve düzenlemek zorundayız. Böylece, veri hattımızı uygulamak, uçtan uca bir makine öğrenimi (ML) projesinin nasıl çalıştığına dair noktaları birleştirecektir.

#Veri Toplama Hattını Tasarlamak ve Uygulamak
Bu bölüm, birden fazla sosyal platformu (Medium, Substack veya GitHub gibi) tarayan ve toplanan verileri bir MongoDB veri ambarına (data warehouse) toplayan bir Ayıkla, Dönüştür, Yükle (Extract, Transform, Load - ETL) hattını nasıl tasarlayacağımızı ve uygulayacağımızı inceleyecektir. Çeşitli tarama yöntemlerini nasıl uygulayacağımızı, verileri nasıl standartlaştıracağımızı ve bir veri ambarına nasıl yükleyeceğimizi göstereceğiz. 
LLM Twin'in veri toplama hattını tasarlayarak ve ETL hattının mimarisini açıklayarak başlayacağız. Ardından, tüm süreci düzenleyecek olan ZenML ile başlayarak hattı uygulamaya başlayacağız. Tarayıcı uygulamasını inceleyeceğiz ve yazılım en iyi uygulamalarını takip ederken, sağlanan bağlantının alanına (domain) göre doğru tarayıcı sınıfını örnekleyen bir dağıtıcı katmanını (dispatcher layer) nasıl uygulayacağımızı anlayacağız. Ardından, her bir tarayıcıyı bireysel olarak nasıl uygulayacağımızı öğreneceğiz. Ayrıca, MongoDB üzerinde tüm belgelerimizi yapılandırmak ve veritabanı ile etkileşimde bulunmak için bir veri katmanını (data layer) nasıl uygulayacağımızı göstereceğiz. Son olarak, ZenML kullanarak veri toplama hattını nasıl çalıştıracağımızı ve MongoDB'den toplanan verileri nasıl sorgulayacağımızı inceleyeceğiz.

#Bölümde İncelenecek Konular
Bu bölümde aşağıdaki konuları inceleyeceğiz:
- LLM Twin'in veri toplama hattını tasarlamak
- LLM Twin'in veri toplama hattını uygulamak
- Ham verileri veri ambarına toplamak

Bu bölümün sonunda, ham verileri ayıklamak (extract), dönüştürmek (transform) ve yüklemek (load) için bir ETL hattını nasıl tasarlayacağınızı ve uygulayacağınızı bileceksiniz, böylece veriler makine öğrenimi uygulamasına hazır hale getirilecektir.

İngilizce teknik terimler parantez içinde eklenmiştir:
- LLM Twin projesini daha derinlemesine incelemek 
- İnce ayar (fine-tuning)
- Çıkarım (inference)
- Veri mühendisliği (data engineering)
- Veri toplama hattı (data collection pipeline)
- Oyuncak projeler (toy projects)
- Statik veri kümesi (static dataset)
- Makine öğrenimi (ML)
- Ayıkla, Dönüştür, Yükle (Extract, Transform, Load - ETL)
- Veri ambarı (data warehouse)
- ZenML 
- Dağıtıcı katmanı (dispatcher layer)
- Veri katmanı (data layer)

Kod bulunmamaktadır, ancak ETL hattının nasıl tasarlanacağı ve uygulanacağı ile ilgili adımlar ayrıntılı olarak açıklanmıştır. 
1. ETL hattını tasarlamak: 
   - Veri kaynaklarını belirlemek (Medium, Substack, GitHub gibi)
   - Veri toplama yöntemlerini belirlemek (tarama, API kullanımı gibi)

2. ETL hattını uygulamak:
   - ZenML kullanarak hattı düzenlemek
   - Tarayıcıları uygulamak
   - Dağıtıcı katmanını uygulamak
   - Veri katmanını uygulamak

3. Ham verileri toplamak:
   - Belirlenen veri kaynaklarından verileri toplamak
   - Toplanan verileri standartlaştırmak
   - Verileri MongoDB veri ambarına yüklemek

4. Veri toplama hattını çalıştırmak:
   - ZenML kullanarak hattı çalıştırmak
   - Toplanan verileri MongoDB'den sorgulamak

---

