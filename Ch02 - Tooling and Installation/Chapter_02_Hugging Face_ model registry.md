#Model Kayıt Merkezi (Model Registry)

Bir model kayıt merkezi (model registry), makine öğrenimi (ML) modellerini tüm yaşam döngüleri boyunca yöneten merkezi bir depodur (repository). Modelleri metadata, sürüm geçmişi (version history) ve performans metrikleri (performance metrics) ile birlikte saklar ve tek bir gerçek kaynağı (single source of truth) olarak hizmet eder. MLOps'ta, model kayıt merkezi, model sürümlerini izlemek, paylaşmak ve belgelemek için kritik öneme sahiptir ve takım işbirliğini kolaylaştırır. Ayrıca, sürekli entegrasyon (continuous integration) ve sürekli dağıtım (continuous deployment) (CI/CD) boru hatları (pipelines) ile entegrasyon sağladığından dağıtım sürecinde temel bir unsurdur.

```markdown
#Model Kayıt Merkezi (Model Registry)
Bir model kayıt merkezi, makine öğrenimi (ML) modellerini tüm yaşam döngüleri boyunca yöneten merkezi bir depodur. Modelleri metadata, sürüm geçmişi ve performans metrikleri ile birlikte saklar ve tek bir gerçek kaynağı olarak hizmet eder. MLOps'ta, model kayıt merkezi, model sürümlerini izlemek, paylaşmak ve belgelemek için kritik öneme sahiptir ve takım işbirliğini kolaylaştırır. Ayrıca, sürekli entegrasyon (continuous integration) ve sürekli dağıtım (continuous deployment) (CI/CD) boru hatları ile entegrasyon sağladığından dağıtım sürecinde temel bir unsurdur.
```

Kod açıklamaları:

* `#Model Kayıt Merkezi (Model Registry)`: Başlık, model kayıt merkezi kavramını tanımlar.
* `Bir model kayıt merkezi, makine öğrenimi (ML) modellerini tüm yaşam döngüleri boyunca yöneten merkezi bir depodur.`: Model kayıt merkezinin tanımı.
* `Modelleri metadata, sürüm geçmişi (version history) ve performans metrikleri (performance metrics) ile birlikte saklar`: Model kayıt merkezinin sakladığı bilgiler.
* `tek bir gerçek kaynağı (single source of truth) olarak hizmet eder`: Model kayıt merkezinin rolü.
* `MLOps'ta, model kayıt merkezi, model sürümlerini izlemek, paylaşmak ve belgelemek için kritik öneme sahiptir`: Model kayıt merkezinin MLOps'taki önemi.
* `takım işbirliğini kolaylaştırır`: Model kayıt merkezinin takım işbirliğine katkısı.
* `sürekli entegrasyon (continuous integration) ve sürekli dağıtım (continuous deployment) (CI/CD) boru hatları ile entegrasyon sağladığından`: Model kayıt merkezinin CI/CD boru hatları ile entegrasyonu.

İngilizce paragrafın birebir Türkçe çevirisi:

Bir model kayıt merkezi, makine öğrenimi modellerini tüm yaşam döngüleri boyunca yöneten merkezi bir depodur. Modelleri metadata, sürüm geçmişi ve performans metrikleri ile birlikte saklar ve tek bir gerçek kaynağı olarak hizmet eder. MLOps'ta, model kayıt merkezi, model sürümlerini izlemek, paylaşmak ve belgelemek için kritik öneme sahiptir ve takım işbirliğini kolaylaştırır. Ayrıca, sürekli entegrasyon ve sürekli dağıtım (CI/CD) boru hatları ile entegrasyon sağladığından dağıtım sürecinde temel bir unsurdur.

Biz Hugging Face'i model kayıt merkezi olarak kullandık, çünkü ekosisteminden yararlanarak ince ayarlı (fine-tuned) LLM Twin modellerimizi kitabı okuyan herkesle kolayca paylaşabiliriz. Ayrıca, Hugging Face model kayıt merkezi arayüzünü takip ederek, modeli LLMs ekosistemindeki tüm çerçevelerle (frameworks) kolayca entegre edebiliriz, örneğin ince ayar için Unsloth ve çıkarım (inference) için SageMaker. İnce ayarlı LLM'lerimiz Hugging Face'te mevcuttur: TwinLlama 3.1 8B (ince ayardan sonra): https://huggingface.co/mlabonne/TwinLlama-3.1-8B TwinLlama 3.1 8B DPO (tercih hizalamadan sonra): https://huggingface.co/mlabonne/TwinLlama-3.1-8B-DPO

```markdown
#Hugging Face Model Kayıt Merkezi Kullanımı
Biz Hugging Face'i model kayıt merkezi olarak kullandık, çünkü ekosisteminden yararlanarak ince ayarlı LLM Twin modellerimizi kitabı okuyan herkesle kolayca paylaşabiliriz. Ayrıca, Hugging Face model kayıt merkezi arayüzünü takip ederek, modeli LLMs ekosistemindeki tüm çerçevelerle kolayca entegre edebiliriz, örneğin ince ayar için Unsloth ve çıkarım için SageMaker.
```

Kod açıklamaları:

* `Biz Hugging Face'i model kayıt merkezi olarak kullandık`: Hugging Face'in model kayıt merkezi olarak seçilmesi.
* `çünkü ekosisteminden yararlanarak ince ayarlı LLM Twin modellerimizi kitabı okuyan herkesle kolayca paylaşabiliriz`: Hugging Face'in avantajı.
* `Hugging Face model kayıt merkezi arayüzünü takip ederek`: Hugging Face arayüzünün takip edilmesi.
* `modeli LLMs ekosistemindeki tüm çerçevelerle kolayca entegre edebiliriz`: Modelin LLMs ekosistemindeki çerçevelerle entegrasyonu.

Çevirinin devamı:

Çoğu makine öğrenimi aracı, model kayıt merkezi özellikleri sağlar. Örneğin, ZenML, Comet ve SageMaker, ilerideki bölümlerde sunacağımız gibi, kendi model kayıt merkezlerini de sunar. İyi seçeneklerdir, ancak Hugging Face'i sadece ekosistemi nedeniyle seçtik, bu da açık kaynaklı ortamda kolay paylaşılabilirlik ve entegrasyon sağlar. Bu nedenle, genellikle projenizin araçları ve gereksinimleri ile en çok entegre olan model kayıt merkezini seçersiniz.

---

