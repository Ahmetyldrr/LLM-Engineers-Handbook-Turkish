# Kitap Boyunca Makine Öğrenimi Operasyonları (MLOps) ve Büyük Dil Modelleri Operasyonları (LLMOps)

Kitap boyunca, ince ayarlı büyük dil modellerini (LLMs - Large Language Models) paylaşmak ve sürümlemek için bir model kaydı (model registry), ince ayar ve RAG verileri için mantıksal bir özellik deposu (logical feature store) ve tüm makine öğrenimi (ML) ardışık işlemlerini birbirine bağlamak için bir orkestrator gibi makine öğrenimi operasyonları (MLOps) bileşenlerini ve ilkelerini zaten kullandık. Ancak MLOps, yalnızca bu bileşenlerle ilgili değildir; veri toplama, eğitim, test ve dağıtımı otomatikleştirerek bir makine öğrenimi uygulamasını bir sonraki seviyeye taşır. Böylece, MLOps'un nihai amacı, mümkün olduğunca çok şeyi otomatikleştirmek ve kullanıcıların dağılımda bir değişiklik tespit edildiğinde ve modelin yeniden eğitilmesinin gerekli olup olmadığına karar vermek gibi en kritik kararlara odaklanmasına izin vermektir. Peki ya Büyük Dil Modelleri Operasyonları (LLMOps)? MLOps'tan nasıl farklıdır? LLMOps terimi, Büyük Dil Modellerinin (LLMs) yaygın olarak benimsenmesinin bir ürünüdür. Geliştirme operasyonları (DevOps) üzerine kurulan MLOps'un üzerine kuruludur. Bu nedenle, LLMOps'un ne hakkında olduğunu tam olarak anlamak için, DevOps'tan başlayarak tarihsel bir bağlam sağlamak zorundayız - ki bu bölümün yapacağı şey tam olarak budur.

# LLMOps'un Temelleri

LLMOps, özünde, Büyük Dil Modellerine (LLMs) özgü sorunlara odaklanır, örneğin istem izleme ve sürümleme (prompt monitoring and versioning), toksik davranışları önlemek için girdi ve çıktı korkulukları (input and output guardrails) ve ince ayar verilerini toplamak için geri bildirim döngüleri (feedback loops). Ayrıca, trilyonlarca token toplamak, büyük GPU kümelerinde modelleri eğitmek ve altyapı maliyetlerini azaltmak gibi Büyük Dil Modelleriyle (LLMs) çalışırken ortaya çıkan ölçekleme sorunlarına da odaklanır. Neyse ki sıradan insanlar için, bu sorunlar çoğunlukla Llama model ailesini sağlayan Meta gibi temel modelleri ince ayarlayan birkaç şirket tarafından çözülür. Çoğu şirket, önceden eğitilmiş temel modelleri kendi kullanım durumları için benimseyecek ve istem izleme ve sürümleme gibi LLMOps sorunlarına odaklanacaktır.

# LLM Twin Kullanım Durumuna LLMOps Eklemek

Uygulama tarafında, LLM Twin kullanım durumumuza LLMOps eklemek için, tüm ZenML ardışık işlemlerini AWS'ye dağıtacağız. Kodumuzun bütünlüğünü test etmek ve dağıtım sürecini otomatikleştirmek için sürekli entegrasyon ve sürekli dağıtım (CI/CD - Continuous Integration/Continuous Deployment) ardışık işlemi, eğitimimizi otomatikleştirmek için sürekli eğitim (CT - Continuous Training) ardışık işlemi ve tüm istemlerimizi ve oluşturulan cevapları izlemek için bir izleme ardışık işlemi uygulayacağız. Bu, Büyük Dil Modelleri (LLMs) kullanıp kullanmadığınıza bakılmaksızın herhangi bir makine öğrenimi projesinde doğal bir ilerlemedir.

Önceki bölümlerde, bir Büyük Dil Modeli uygulaması oluşturmayı öğrendiniz. Şimdi, LLMOps ile ilgili üç ana hedefi keşfetme zamanı. Birincisi, DevOps'tan başlayarak, ardından MLOps'un temel ilkelerine geçerek ve son olarak LLMOps'a girerek LLMOps'un teorik bir anlayışını kazanmaktır. DevOps, MLOps ve LLMOps üzerine tüm teoriyi sunmayı amaçlamıyoruz, çünkü bu konularda kolayca bir kitap yazabilirsiniz. Ancak, LLM Twin kullanım durumunu uygularken belirli kararlar vermenin nedenlerini güçlü bir şekilde anlamak istiyoruz.

İkinci hedefimiz, ZenML ardışık işlemlerini AWS'ye dağıtmak (şu anda, yalnızca çıkarım ardışık işlemimizi 10. Bölüm'de AWS'ye dağıttık). Bu bölüm, ZenML'yi her şeyi AWS'ye dağıtmak için nasıl kullanacağınızı gösterecek pratik bir bölüm olacaktır. Bunu, üçüncü ve son hedefimizi uygulamak için yapmamız gerekiyor, ki bu da teorik bölümde öğrendiklerimizi LLM Twin kullanım durumumuza uygulamaktır. GitHub Actions kullanarak bir CI/CD ardışık işlemi, ZenML kullanarak bir CT ve uyarı ardışık işlemi ve Comet ML'den Opik kullanarak bir izleme ardışık işlemi uygulayacağız.

Böylece, bu bölümde aşağıdaki konuları ele alacağız:
- # LLMOps'a Yol: DevOps ve MLOps'taki Köklerini Anlamak
- # LLM Twin'in Ardışık İşlemlerini Buluta Dağıtma
- # LLM Twin'e LLMOps Eklemek

Kod Açıklaması:
```markdown
### Kodlar
#### CI/CD Pipeline (Sürekli Entegrasyon/Sürekli Dağıtım Ardışık İşlemi)
```yml
# .github/workflows/cicd.yml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Login to AWS
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: 'us-east-1'

      - name: Deploy to AWS
        run: |
          # AWS'ye dağıtım komutları
```

#### CT (Sürekli Eğitim) Pipeline
```python
# ct_pipeline.py
from zenml import pipeline

@pipeline
def ct_pipeline():
    # Eğitim ardışık işlem adımları
    pass
```

#### Monitoring (İzleme) Pipeline
```python
# monitoring_pipeline.py
from zenml import pipeline

@pipeline
def monitoring_pipeline():
    # İzleme ardışık işlem adımları
    pass
```

### Kodların Ayrıntılı Açıklaması

1. **CI/CD Ardışık İşlemi (`.github/workflows/cicd.yml`):**
   - Bu, GitHub Actions kullanarak tanımlanan bir CI/CD ardışık işlemidir.
   - `on.push.branches.main` ifadesi, ana dala (`main`) yapılan her `push` işleminde tetiklenir.
   - `jobs.deploy` altında, `ubuntu-latest` üzerinde çalışan bir iş tanımlanmıştır.
   - `steps` altında sırasıyla kodun checkout edilmesi, AWS'ye giriş yapılması ve AWS'ye dağıtım işlemleri gerçekleştirilir.

2. **CT (Sürekli Eğitim) Ardışık İşlemi (`ct_pipeline.py`):**
   - ZenML kütüphanesini kullanarak bir sürekli eğitim ardışık işlemi tanımlanmıştır.
   - `@pipeline` dekoratörü, bu fonksiyonun bir ardışık işlem olduğunu belirtir.
   - Fonksiyon içinde, eğitim ardışık işlem adımları tanımlanacaktır.

3. **İzleme Ardışık İşlemi (`monitoring_pipeline.py`):**
   - Yine ZenML kullanarak bir izleme ardışık işlemi tanımlanmıştır.
   - Bu ardışık işlem, istemleri ve oluşturulan cevapları izlemek için kullanılacaktır.

Bu kodlar, LLM Twin kullanım durumuna LLMOps eklemek için temel bileşenleri oluşturur. CI/CD ardışık işlemleri kodun bütünlüğünü sağlar ve dağıtımı otomatikleştirir, CT ardışık işlemleri eğitimi otomatikleştirir ve izleme ardışık işlemleri sistemin sağlığını izler.

---

