#Bulut Üzerinde LLM Twin'in İşlem Hatlarını Dağıtma

Bu bölüm, LLM Twin'in tüm işlem hatlarını (pipelines) buluta nasıl dağıtacağınızı gösterecektir. Tüm sistemin bulutta çalışmasını sağlamak için tüm altyapıyı dağıtmalıyız. Böylece, şunları yapmak zorunda kalacağız: 
- MongoDB sunucusuz (serverless) bir örneğini kurmak. 
- Qdrant sunucusuz (serverless) bir örneğini kurmak. 
- ZenML işlem hatlarını (pipelines), konteyner (container) ve artifact kayıt defterini (artifact registry) AWS'ye dağıtmak. 
- Kodları konteyner içine almak (containerize) ve Docker imajını (Docker image) bir konteyner kayıt defterine (container registry) göndermek. 
- Eğitim ve çıkarım (inference) işlem hatlarının zaten AWS SageMaker ile çalıştığını unutmayın. 
Böylece, önceki dört adımı takip ederek, tüm sistemimizin bulutta, ölçeklendirmeye ve varsayımsal müşterilerimize hizmet etmeye hazır olmasını sağlarız.

#Dağıtım Maliyetleri Nelerdir?
MongoDB, Qdrant ve ZenML hizmetlerinin ücretsiz sürümlerine bağlı kalacağız. AWS için ise ZenML işlem hatlarını çalıştırmak için çoğunlukla ücretsiz katmanlarına (free tier) bağlı kalacağız. SageMaker eğitim ve çıkarım bileşenlerinin çalıştırılması daha maliyetlidir (bu bölümde çalıştırmayacağız). Böylece, aşağıdaki bölümlerde size göstereceklerimiz AWS'den minimum maliyetler (en fazla birkaç dolar) üretecektir.

Kod açıklamaları için ilgili kod satırları bulunmamaktadır. Lütfen ilgili kodları paylaşırsanız, satır satır ayrıntılı olarak açıklayabilirim. 

İngilizce teknik terimlerin Türkçeleri parantez içinde eklenmiştir. 

İlgili metnin orijinal hali:
```
This section will show you how to deploy all the LLM Twin’s pipelines to the cloud. 
We must deploy the entire infrastructure to have the whole system working in the cloud. 
Thus, we will have to: 
Set up an instance of MongoDB serverless. 
Set up an instance of Qdrant serverless. 
Deploy the ZenML pipelines, container, and artifact registry to AWS. 
Containerize the code and push the Docker image to a container registry. 
Note that the training and inference pipelines already work with AWS SageMaker. 
Thus, by following the preceding four steps, we ensure that our whole system is on the cloud, ready to scale and serve our imaginary clients. 
What are the deployment costs? 
We will stick to the free versions of the MongoDB, Qdrant, and ZenML services. 
As for AWS, we will mostly stick to their free tier for running the ZenML pipelines. 
The SageMaker training and inference components are more costly to run (which we won’t run in this section). 
Thus, what we will show you in the following sections will generate minimum costs (a few dollars at most) from AWS.
```

Türkçe çevirisi:
```
#Bulut Üzerinde LLM Twin'in İşlem Hatlarını Dağıtma

Bu bölüm, LLM Twin'in tüm işlem hatlarını (pipelines) buluta nasıl dağıtacağınızı gösterecektir. 
Tüm sistemin bulutta çalışmasını sağlamak için tüm altyapıyı dağıtmalıyız. 
Böylece, şunları yapmak zorunda kalacağız: 
- MongoDB sunucusuz (serverless) bir örneğini kurmak. 
- Qdrant sunucusuz (serverless) bir örneğini kurmak. 
- ZenML işlem hatlarını (pipelines), konteyner (container) ve artifact kayıt defterini (artifact registry) AWS'ye dağıtmak. 
- Kodları konteyner içine almak (containerize) ve Docker imajını (Docker image) bir konteyner kayıt defterine (container registry) göndermek. 
- Eğitim ve çıkarım (inference) işlem hatlarının zaten AWS SageMaker ile çalıştığını unutmayın. 
Böylece, önceki dört adımı takip ederek, tüm sistemimizin bulutta, ölçeklendirmeye ve varsayımsal müşterilerimize hizmet etmeye hazır olmasını sağlarız.

#Dağıtım Maliyetleri Nelerdir? 
MongoDB, Qdrant ve ZenML hizmetlerinin ücretsiz sürümlerine bağlı kalacağız. 
AWS için ise ZenML işlem hatlarını çalıştırmak için çoğunlukla ücretsiz katmanlarına (free tier) bağlı kalacağız. 
SageMaker eğitim ve çıkarım bileşenlerinin çalıştırılması daha maliyetlidir (bu bölümde çalıştırmayacağız). 
Böylece, aşağıdaki bölümlerde size göstereceklerimiz AWS'den minimum maliyetler (en fazla birkaç dolar) üretecektir.
```

---

