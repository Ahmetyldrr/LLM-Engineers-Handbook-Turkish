#Handler Sınıf Hiyerarşisi ve Etkileşimi

İşleyici (Handler), alanımızla bire bir yapıya sahiptir, yani her bir varlık (entity) kendi işleyicisine (handler) sahiptir, Şekil 8.17'de gösterildiği gibi. Toplamda, aşağıdaki temel arayüzleri (base interfaces) takip eden dokuz İşleyici (Handler) sınıfına sahip olacağız: 
class CleaningDataHandler() 
class ChunkingDataHandler() 
class EmbeddingDataHandler() 

#Şekil 4.17: Handler sınıf hiyerarşisi ve etkileşimi

Tüm işleyicilere (handlers) ait kod GitHub'da https://github.com/PacktPublishing/LLM-Engineering/tree/main/llm_engineering/application/preprocessing adresinde mevcuttur. Her bir işleyici ailesini (handler family) inceleyelim ve nasıl uygulandığını görelim.

**Kod Açıklaması**

1. `class CleaningDataHandler()`: 
   - Bu satır, `CleaningDataHandler` adlı bir sınıf tanımlar. 
   - Bu sınıf, muhtemelen veri temizleme (data cleaning) işlemlerini gerçekleştirmek için kullanılır.

2. `class ChunkingDataHandler()`: 
   - Bu satır, `ChunkingDataHandler` adlı bir sınıf tanımlar. 
   - Bu sınıf, muhtemelen veri parçalama (data chunking) işlemlerini gerçekleştirmek için kullanılır.

3. `class EmbeddingDataHandler()`: 
   - Bu satır, `EmbeddingDataHandler` adlı bir sınıf tanımlar. 
   - Bu sınıf, muhtemelen veri gömme (data embedding) işlemlerini gerçekleştirmek için kullanılır.

Tüm bu sınıflar, işleyici (Handler) sınıf hiyerarşisinin bir parçasıdır ve her biri belirli bir veri ön işleme (preprocessing) görevini yerine getirir.

**İngilizce Teknik Terimlerin Türkçeleri ve Parantez İçinde Eklenmesi**

- handler (İşleyici)
- domain (Alan)
- entity (Varlık)
- base interfaces (Temel Arayüzler)
- class (Sınıf)

Paragrafın tamamen aynı Türkçeye çevirisi:
#Handler Sınıf Hiyerarşisi ve Etkileşimi (Handler Class Hierarchy and their Interaction)

İşleyici (Handler), alanımızla (domain) bire bir yapıya sahiptir, yani her bir varlık (entity) kendi işleyicisine (handler) sahiptir, Şekil 8.17'de gösterildiği gibi. Toplamda, aşağıdaki temel arayüzleri (base interfaces) takip eden dokuz İşleyici (Handler) sınıfına (class) sahip olacağız: 
class CleaningDataHandler() 
class ChunkingDataHandler() 
class EmbeddingDataHandler() 

#Şekil 4.17: Handler Sınıf Hiyerarşisi ve Etkileşimi (Figure 4.17: Handler class hierarchy and their interaction)

Tüm işleyicilere (handlers) ait kod GitHub'da https://github.com/PacktPublishing/LLM-Engineering/tree/main/llm_engineering/application/preprocessing adresinde mevcuttur. Her bir işleyici ailesini (handler family) inceleyelim ve nasıl uygulandığını görelim.

---

