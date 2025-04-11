# Selenium ile İlgili Sorunların Çözümü

Selenium'un çalıştırılmasının, ChromeDriver gibi tarayıcı sürücüsü (browser driver) ile ilgili sorunlar nedeniyle sorunlara neden olduğu iyi bilinen bir konudur (issue). Bu nedenle, MediumCrawler gibi Selenium kullanan tarayıcılar (crawlers), ChromeDriver ile ilgili sorunlar nedeniyle başarısız olursa (fail), veri toplama YAML konfigürasyonlarına (data collection YAML configs) eklenen Medium bağlantılarını (Medium links) yorum satırına alarak (commenting out) bu sorunu kolayca aşabilirsiniz. Bunu yapmak için, `configs/` dizinine (directory) gidin ve `digital_data_etl_*` ile başlayan tüm YAML dosyalarını (YAML files), örneğin `digital_data_etl_maxime_labonne.yaml` dosyasını bulun. Bunları açın ve Şekil 3.7'de gösterildiği gibi Medium ile ilgili tüm URL'leri (Medium-related URLs) yorum satırına alın. Substack veya kişisel blog URL'lerini (Substack or personal blog URLs) olduğu gibi bırakabilirsiniz, çünkü bunlar Selenium'a bağımlı olmayan (not dependent on Selenium) CustomArticleCrawler'ı kullanır. Şekil 3.7: Ham verileri tararken (crawling raw data) Selenium sorunlarını düzeltme (Fix Selenium issues).

**İngilizce Teknik Terimlerin Türkçeleri ve Açıklamaları Parantez İçinde:**
- issue (sorun, konudur)
- browser driver (tarayıcı sürücüsü)
- crawlers (veri tarayıcıları)
- fail (başarısız olmak)
- data collection YAML configs (veri toplama YAML konfigürasyonları)
- commenting out (yorum satırına almak)
- directory (dizin)
- YAML files (YAML dosyaları)

**Kodların Ayrıntılı Açıklaması:**
1. `configs/` dizinine gidin: Bu adımda, projenin konfigürasyon dosyalarının bulunduğu dizine gidilmesi istenir.
   
2. `digital_data_etl_*` ile başlayan YAML dosyalarını bulun: Burada, belirli bir isimlendirme kuralına (pattern) göre YAML dosyaları aranmaktadır. `digital_data_etl_` ile başlayan tüm dosyalar hedeflenmektedir.

3. Bu YAML dosyalarını açın ve Medium ile ilgili URL'leri yorum satırına alın: İlgili YAML dosyalarında, Medium platformuna ait URL'ler `#` işareti kullanılarak yorum satırına alınmalıdır. Bu sayede, Medium'a ait veri tarama işlemleri geçici olarak devre dışı bırakılır.

4. Substack veya kişisel blog URL'lerini olduğu gibi bırakın: Çünkü bu tür URL'ler Selenium'a bağımlı olmayan CustomArticleCrawler tarafından işlenir.

**Kodlar:**
```bash
# configs/ dizinine gidin
cd configs/

# digital_data_etl_* ile başlayan YAML dosyalarını listeleyin
ls digital_data_etl_*.yaml

# Örnek bir YAML dosyasını düzenlemek için açın
nano digital_data_etl_maxime_labonne.yaml

# İçerikte Medium ile ilgili URL'leri yorum satırına alın
# Örneğin:
#   - https://medium.com/ornek1
#   Şeklinde olan bir satırı aşağıdaki gibi yorum satırına alın
#   - #https://medium.com/ornek1

# Dosyayı kaydedip çıkın
# Substack veya kişisel blog URL'lerini olduğu gibi bırakın
```

---

