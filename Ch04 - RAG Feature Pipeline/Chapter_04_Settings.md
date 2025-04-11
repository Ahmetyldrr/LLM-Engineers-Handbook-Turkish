# Pydantic Ayarları Kullanımı
Pydantic Ayarları (https://docs.pydantic.dev/latest/concepts/pydantic_settings/) kullanarak, hassas veya hassas olmayan değişkenleri bir `.env` dosyasından yükleyen global bir Ayarlar (Settings) sınıfı tanımlarız. Bu yaklaşım aynı zamanda bize Pydantic'in tüm avantajlarını sağlar, örneğin tip doğrulama (type validation). Örneğin, `QDRANT_DATABASE_PORT` değişkeni için bir tamsayı yerine bir dize (string) sağlarsak, program çöker. Bu davranış tüm uygulamayı daha deterministik ve güvenilir kılar. RAG özellik ardışık düzenini (feature pipeline) oluşturmak için gerekli tüm değişkenlerle Ayarlar sınıfı aşağıdaki gibidir:

```python
from pydantic import BaseSettings

class Settings(BaseSettings):
    class Config:
        env_file = ".env"
        env_file_encoding = "utf-8"

    # Diğer bazı ayarlar...
    # RAG 
    TEXT_EMBEDDING_MODEL_ID: str = "sentence-transformers/all-MiniLM-L6-v2"
    RERANKING_CROSS_ENCODER_MODEL_ID: str = "cross-encoder/ms-marco-MiniLM-L-4-v2"
    RAG_MODEL_DEVICE: str = "cpu"

    # QdrantDB Vektör Veritabanı (Vector DB)
    USE_QDRANT_CLOUD: bool = False
    QDRANT_DATABASE_HOST: str = "localhost"
    QDRANT_DATABASE_PORT: int = 6333
    QDRANT_CLOUD_URL: str = "str"
    QDRANT_APIKEY: str | None = None

    # Daha fazla ayar...
settings = Settings()
```

İç `Config` sınıfında belirtildiği gibi, tüm değişkenlerin varsayılan değerleri vardır veya bir `.env` dosyası sağlanarak geçersiz kılınabilirler.

### Kod Açıklaması

1. `from pydantic import BaseSettings`: Pydantic kütüphanesinden `BaseSettings` sınıfını içe aktarır. Bu sınıf, uygulama ayarlarını tanımlamak için kullanılır.

2. `class Settings(BaseSettings)`: `BaseSettings` sınıfından miras alan `Settings` adlı bir sınıf tanımlar. Bu sınıf, uygulamanın ayarlarını içerir.

3. `class Config`: `Settings` sınıfının içinde iç içe bir `Config` sınıfı tanımlar. Bu sınıf, ayarların nasıl yükleneceği konusunda yapılandırma sağlar.

4. `env_file = ".env"`: `.env` dosyasının yolunu belirtir. Bu dosya, ayarların değerlerini içerir.

5. `env_file_encoding = "utf-8"`: `.env` dosyasının kodlamasını belirtir.

6. `TEXT_EMBEDDING_MODEL_ID`, `RERANKING_CROSS_ENCODER_MODEL_ID`, `RAG_MODEL_DEVICE`, `USE_QDRANT_CLOUD`, `QDRANT_DATABASE_HOST`, `QDRANT_DATABASE_PORT`, `QDRANT_CLOUD_URL`, `QDRANT_APIKEY`: Bunlar, RAG özellik ardışık düzenini ve QdrantDB Vektör Veritabanı ayarlarını tanımlayan değişkenlerdir. Her birinin bir varsayılan değeri vardır.

7. `settings = Settings()`: `Settings` sınıfının bir örneğini oluşturur ve `settings` değişkenine atar. Bu, ayarları kullanıma hazır hale getirir.

Bu kod, Pydantic'in sağladığı tip doğrulama ve `.env` dosyası yükleme özelliklerini kullanarak uygulamanın ayarlarını yönetir. Bu, uygulamanın daha güvenilir ve deterministik olmasını sağlar. 

İngilizce teknik terimlerin Türkçeleri parantez içinde eklenmiştir:
- Pydantic Settings (Pydantic Ayarları)
- type validation (tip doğrulama)
- feature pipeline (özellik ardışık düzeni)
- Vector DB (Vektör Veritabanı)

---

