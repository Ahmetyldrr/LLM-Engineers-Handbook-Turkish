# Şimdiye Kadar Tanımladığımız Altyapı
Şimdiye kadar, depolama ve hesaplama için MongoDB, Qdrant ve AWS altyapımızı tanımladık. Son adım, kodumuzu alıp bu altyapının üzerine çalıştırmaktır. En popüler çözüm, Docker'dır. Docker, bir uygulama (container) oluşturmamızı sağlar ve bu uygulama içerisinde sistem bağımlılıkları, Python bağımlılıkları ve kod gibi her şeyi içerir.

# Dockerfile Tanımı
Docker imajımızı projenin kök dizininde Dockerfile içinde tanımladık. Bu, Docker için standart adlandırma kuralıdır. Koda dalmadan önce, Docker imajını kendiniz oluşturmak istiyorsanız, makinenizde Docker'ın yüklü olduğundan emin olun. Eğer yüklü değilse, buradaki talimatları takip ederek yükleyebilirsiniz: https://docs.docker.com/engine/install.

## Dockerfile İçeriği
Şimdi, Dockerfile'ın içeriğine adım adım bakalım.

### 1. Temel İmaja (Base Image) Başlama
Dockerfile, Debian Bullseye dağıtımı üzerine kurulmuş Python 3.11'un hafifletilmiş bir versiyonu olan temel imajı belirterek başlar.
```dockerfile
FROM python:3.11-slim-bullseye AS release
```
- `FROM`: Yeni bir imaj oluşturmak için temel olarak kullanılan imajı belirtir.
- `python:3.11-slim-bullseye`: Python 3.11'un Debian Bullseye tabanlı hafif imajı.

### 2. Ortam Değişkenlerini Tanımlama
Ortam değişkenleri, konteynırın çeşitli yönlerini yapılandırmak için ayarlanır.
```dockerfile
ENV WORKSPACE_ROOT=/app/
ENV PYTHONDONTWRITEBYTECODE=1 
ENV PYTHONUNBUFFERED=1 
ENV POETRY_VERSION=1.8.3 
ENV DEBIAN_FRONTEND=noninteractive
ENV POETRY_NO_INTERACTION=1
```
- `ENV`: Ortam değişkeni tanımlar.
- `WORKSPACE_ROOT`: Çalışma dizinini `/app/` olarak ayarlar.
- `PYTHONDONTWRITEBYTECODE`: Python bytecode oluşturmayı kapatır.
- `PYTHONUNBUFFERED`: Python'u doğrudan terminale çıktı vermeye yapılandırır.
- `POETRY_VERSION`: Yüklenecek Poetry sürümünü belirtir.
- `DEBIAN_FRONTEND=noninteractive`: Paket kurulumlarını etkileşimli olmayan modda yapar.
- `POETRY_NO_INTERACTION=1`: Poetry'i etkileşimli olmayan modda çalıştırır.

### 3. Google Chrome Kurulumu
Konteynır içine Google Chrome kurulur.
```dockerfile
RUN apt-get update -y && \
    apt-get install -y gnupg wget curl --no-install-recommends && \
    wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | gpg --dearmor -o /usr/share/keyrings/google-linux-signing-key.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/google-linux-signing-key.gpg] https://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google-chrome.list && \
    apt-get update -y && \
    apt-get install -y google-chrome-stable && \
    rm -rf /var/lib/apt/lists/*
```
- `RUN`: Docker imajı oluşturulurken bir komut çalıştırır.
- `apt-get update` ve `apt-get install`: Paket listelerini günceller ve gerekli paketleri kurar.
- Google Chrome için gerekli anahtarı ve repository'i ekler.

### 4. Diğer Sistem Bağımlılıklarının Kurulumu
Diğer gerekli sistem bağımlılıkları kurulur ve paket önbelleği temizlenir.
```dockerfile
RUN apt-get update -y \
    && apt-get install -y --no-install-recommends build-essential \
    gcc \
    python3-dev \
    build-essential \
    libglib2.0-dev \
    libnss3-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
```
- Gerekli geliştirme araçları ve kütüphaneler kurulur.

### 5. Poetry Kurulumu
Poetry, pip kullanılarak kurulur ve yapılandırılır.
```dockerfile
RUN pip install --no-cache-dir "poetry==$POETRY_VERSION"
RUN poetry config installer.max-workers 20
```
- `pip install`: Poetry'i belirtilen sürümde kurar.
- `poetry config`: Poetry'i en fazla 20 paralel çalışan ile paket kurulumu yapacak şekilde yapılandırır.

### 6. Proje Bağımlılıklarının Tanımlanması ve Kurulumu
Proje bağımlılıkları `pyproject.toml` ve `poetry.lock` dosyaları üzerinden tanımlanır ve kurulur.
```dockerfile
WORKDIR $WORKSPACE_ROOT
COPY pyproject.toml poetry.lock $WORKSPACE_ROOT
RUN poetry config virtualenvs.create false && \
    poetry install --no-root --no-interaction --no-cache --without dev && \
    poetry self add 'poethepoet[poetry_plugin]' && \
    rm -rf ~/.cache/pypoetry/cache/ && \
    rm -rf ~/.cache/pypoetry/artifacts/
```
- `WORKDIR`: Çalışma dizinini ayarlar.
- `COPY`: `pyproject.toml` ve `poetry.lock` dosyalarını konteynır içine kopyalar.
- `poetry install`: Proje bağımlılıklarını kurar.

### 7. Proje Dosyalarının Kopyalanması
Tüm proje dosyaları konteynır içine kopyalanır.
```dockerfile
COPY . $WORKSPACE_ROOT
```
- Proje dosyaları çalışma dizinine kopyalanır.

## Docker İmagesinin Oluşturulması ve ECR'ye Yüklenmesi
Docker imajı oluşturulur ve Amazon ECR'ye yüklenir.
```bash
docker buildx build --platform linux/amd64 -t llmtwin -f Dockerfile .
AWS_REGION=<your_region>
AWS_ECR_URL=<your_account_id>
aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ECR_URL}
docker tag llmtwin ${AWS_ECR_URL}:latest
docker push ${AWS_ECR_URL}:latest
```
- `docker buildx build`: Docker imajını oluşturur.
- `aws ecr get-login-password` ve `docker login`: ECR'ye giriş yapar.
- `docker tag`: Oluşturulan imaja ECR URL'sini içeren bir etiket ekler.
- `docker push`: İmaji ECR'ye iter.

Bu adımların ardından Docker imajı ECR'ye başarıyla yüklenir ve AWS üzerinde deploy edilebilir hale gelir.

---

