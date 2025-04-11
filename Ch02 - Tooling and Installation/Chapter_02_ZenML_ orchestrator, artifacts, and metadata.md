# ZenML: Makine Öğrenimi ve MLOps Arasındaki Köprü

ZenML, ML (Makine Öğrenimi) ve MLOps arasında köprü görevi görür. Böylece, ML pipeline'ınızın izlenebilirlik (traceability), tekrarlanabilirlik (reproducibility), dağıtım (deployment) ve sürdürülebilirlik (maintainability) özelliklerini kolaylaştıran birden fazla MLOps özelliği sunar. Çekirdeğinde, makine öğreniminde tekrarlanabilir iş akışları (reproducible workflows) oluşturmak üzere tasarlanmıştır. Jupyter not defterlerindeki keşifsel araştırmadan üretim hazır (production-ready) bir ML ortamına geçiş zorluğunu ele alır. Versiyonlama zorlukları, deneyleri yeniden üretme, karmaşık ML iş akışlarını organize etme, eğitim ve dağıtım arasındaki boşluğu doldurma ve meta verileri (metadata) izleme gibi üretim tabanlı çoğaltma sorunlarını çözer. Böylece, ZenML'nin ana özellikleri ML pipeline'larını düzenlemek (orchestrating), ML pipeline'larını çıktı olarak depolamak ve versiyonlamak, ve daha iyi gözlemlenebilirlik (observability) için artifact'lere meta veri eklemektir.

ZenML, başka bir ML platformu olmak yerine, birden fazla altyapı seçeneğinde ZenML'yi çalıştırmanıza olanak tanıyan "stack" kavramını tanıttı. Bir stack, ZenML'yi farklı bulut hizmetlerine bağlamanıza olanak tanır, örneğin:
- Bir düzenleyici (orchestrator) ve hesaplama motoru (compute engine) (örneğin, AWS SageMaker veya Vertex AI)
- Uzaktan depolama (remote storage) (örneğin, AWS S3 veya Google Cloud Storage bucket'ları)
- Bir konteyner kayıt defteri (container registry) (örneğin, Docker Registry veya AWS ECR)

Böylece ZenML, stack özelliği aracılığıyla tüm altyapınızı ve araçlarınızı tek bir yerde bir araya getiren bir yapıştırıcı görevi görür, böylece geliştirme süreçlerinizde hızlı bir şekilde yineleme yapmanıza ve tüm ML sisteminizi kolayca izlemenize olanak tanır. Bunun güzelliği, ZenML'nin sizi herhangi bir bulut platformuna kilitlememesidir (vendor-lock). Python kodunuzun uygulanmasını, üzerinde çalıştığı altyapıdan tamamen soyutlar. Örneğin, LLM Twin kullanım durumumuzda, AWS stack'ini kullandık:
- SageMaker'ı düzenleyici ve hesaplama olarak
- S3'ü artifact'ları depolamak ve izlemek için uzaktan depolama olarak
- ECR'yi konteyner kayıt defteri olarak

Ancak Python kodu, ZenML'nin bunları halletmesi nedeniyle S3 veya ECR'ye özgü hiçbir şey içermez. Böylece, Google Cloud Storage veya Azure gibi diğer sağlayıcılara kolayca geçebiliriz. ZenML stack'leri hakkında daha fazla ayrıntı için buradan başlayabilirsiniz: https://docs.zenml.io/user-guide/production-guide/understand-stacks.

Bu kitapta, düzenleme (orchestrating), artifact'lar ve meta veriler gibi ZenML özelliklerine odaklanacağız. ZenML hakkında daha fazla ayrıntı için, başlangıç kılavuzlarına göz atın: https://docs.zenml.io/user-guide/starter-guide.

ZenML sunucusunun yerel versiyonu, bir Python paketi olarak gelir. Böylece, `poetry install` çalıştırdığınızda, yerel olarak kullanabileceğiniz bir ZenML hata ayıklama sunucusu kurar. 11. Bölüm'de, ML pipeline'larını AWS'ye dağıtmak için onların bulut sunucusuz (cloud serverless) seçeneğini nasıl kullanacağınızı göstereceğiz.

### Kodların Açıklaması

Bu metinde kod bulunmamaktadır, ancak `poetry install` komutu açıklanabilir.

```bash
poetry install
```

Bu komut, `pyproject.toml` dosyasındaki bağımlılıkları yükler. Bu, ZenML'nin yerel versiyonunu kurmak için kullanılır.

### İngilizce Teknik Terimlerin Türkçe Karşılıkları ve Açıklamaları

- **ML (Machine Learning)**: Makine Öğrenimi
- **MLOps**: Makine Öğrenimi Operasyonları
- **Pipeline**: İşlem Hattı
- **Traceability**: İzlenebilirlik
- **Reproducibility**: Tekrarlanabilirlik
- **Deployment**: Dağıtım
- **Maintainability**: Sürdürülebilirlik
- **Reproducible Workflows**: Tekrarlanabilir İş Akışları
- **Metadata**: Meta Veriler
- **Orchestrating**: Düzenleme
- **Artifact**: Yapıt (Makine öğrenimi bağlamında, bir işlemin çıktısı olan veri veya model)
- **Observability**: Gözlemlenebilirlik
- **Stack**: Yığın (Farklı hizmetleri bir araya getiren yapı)
- **Orchestrator**: Düzenleyici (İş akışlarını yöneten bileşen)
- **Compute Engine**: Hesaplama Motoru (Hesaplamaları gerçekleştiren bileşen)
- **Remote Storage**: Uzaktan Depolama (Verileri uzak sunucularda depolama)
- **Container Registry**: Konteyner Kayıt Defteri (Konteyner görüntülerini depolayan ve yöneten sistem)
- **Vendor-lock**: Tedarikçi Kilidi (Bir tedarikçiye bağlı kalma durumu)

---

