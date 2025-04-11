# AWS SageMaker ve AWS Bedrock Karşılaştırması

Aşağıdaki İngilizce paragrafın birebir Türkçe çevirisi:

Ayrıca, AWS Bedrock gibi daha basit ve daha uygun maliyetli seçenekler yerine neden AWS SageMaker'ı seçtiğimizi tartışmalıyız. İlk olarak, Bedrock ve faydalarını açıklayalım. Amazon Bedrock, LLMs (Large Language Models) dağıtmak için sunucusuz (serverless) bir çözümdür. Sunucusuz, yönetilecek sunucu veya altyapı olmadığı anlamına gelir. API çağrıları (API calls) yoluyla doğrudan erişebileceğiniz önceden eğitilmiş modeller (pre-trained models) sağlar. Bu kitabı yazdığımızda, sadece Mistral, Flan, Llama 2 ve Llama 3 için destek sağlıyorlardı (oldukça sınırlı bir seçenek listesi). Giriş verilerini (input data) gönderebilir ve altta yatan altyapı veya yazılımı yönetmeden modellerden tahminler (predictions) alabilirsiniz. Bu yaklaşım, AI yeteneklerini uygulamalara entegre etmek için gereken karmaşıklığı ve zamanı önemli ölçüde azaltır, makine öğrenimi (machine learning) konusunda sınırlı uzmanlığa sahip geliştiriciler için daha erişilebilir hale getirir. Ancak, bu entegrasyon kolaylığı, Amazon Bedrock tarafından sağlanan önceden eğitilmiş modellere ve API'lere kısıtlı olduğunuz için sınırlı özelleştirme seçenekleri (customization options) maliyetiyle gelir. Fiyatlandırma açısından Bedrock, API çağrılarının sayısına dayanan basit bir fiyatlandırma modeli (pricing model) kullanır. Bu basit fiyatlandırma yapısı, maliyetleri tahmin etmeyi ve kontrol etmeyi daha verimli hale getirir. Bu arada, SageMaker, makine öğrenimi modelleri oluşturmak, eğitmek ve dağıtmak için kapsamlı bir platform sağlar. ML süreçlerinizi tamamen özelleştirebilir veya platformu araştırma için kullanabilirsiniz. Bu nedenle SageMaker, programlamayı bilen, makine öğrenimi kavramlarını anlayan ve AWS gibi bulut platformlarıyla çalışmaktan rahat olan veri bilimciler (data scientists) ve makine öğrenimi uzmanları tarafından 주로 kullanılır. SageMaker, maliyetler açısından çelişkili bir yapıya sahiptir, çoğu AWS hizmetine benzer şekilde kullanılan bir ödemeye göre fiyatlandırma modeli (pay-as-you-go pricing model) izler. Bu, hesaplama kaynaklarının, depolamanın ve uygulamalarınızı oluşturmak için gereken diğer hizmetlerin kullanımı için ödeme yapmanız gerektiği anlamına gelir. Bedrock'un aksine, SageMaker uç noktası (endpoint) kullanılmasa bile, çevrimiçi EC2 örnekleri (instances) gibi AWS'de dağıtılan kaynaklar için yine de ödeme yaparsınız. Bu nedenle, kullanılmayan kaynakları silen otomatik ölçeklendirme sistemleri (autoscaling systems) tasarlamalısınız. Sonuç olarak Bedrock, mevcut temel modellerden (foundation models) biriyle desteklenen bir API uç noktası (API endpoint) hızlı bir şekilde dağıtmanıza olanak tanıyan kullanıma hazır bir çözüm sunar. Bu arada SageMaker, ML mantığınızı tamamen özelleştirmenizi sağlayan çok işlevli bir platformdur. Peki neden Bedrock yerine SageMaker'ı seçtik? Bedrock, bir şeyleri hızlıca prototiplemek için mükemmel bir çözüm olabilirdi, ancak bu kitap LLM mühendisliği hakkındadır ve amacımız Bedrock'un gizlemeye çalıştığı tüm mühendislik yönlerini incelemektir. Bu nedenle, bir modeli dağıtmak için gereken tüm mühendisliği size gösterebilmemizi sağlayan yüksek düzeyde özelleştirilebilirlik (customizability) nedeniyle SageMaker'ı seçtik. Gerçekte, SageMaker bile tamamen özelleştirilebilir değildir. Dağıtımınız üzerinde tam kontrol sahibi olmak istiyorsanız, AWS'nin Kubernetes kendi kendine yönetilen hizmeti olan EKS'yi (Elastic Kubernetes Service) kullanın. Bu durumda, sanal makinelere (virtual machines) doğrudan erişiminiz olur, böylece ML işlem hatlarınızı (pipelines) nasıl oluşturduğunuzu, nasıl etkileşimde bulunduğunu ve kaynaklarınızı nasıl yönettiğinizi tamamen özelleştirebilirsiniz. Aynı şeyi AWS ECS (EC2 Container Service) ile de yapabilirsiniz, AWS'nin Kubernetes versiyonu. EKS veya ECS kullanarak maliyetleri de azaltabilirsiniz, çünkü bu hizmetler önemli ölçüde daha az maliyetlidir. Sonuç olarak SageMaker, tam kontrol ve özelleştirme ile tüm mühendislik karmaşıklığını sahne arkasında gizleyen tam olarak yönetilen bir hizmet arasında bir denge kurar. Bu denge, yönetilen hizmetin rahatlığından da yararlanırken ihtiyacınız olan kontrole sahip olmanızı sağlar.

İngilizce teknik terimlerin Türkçe çevirilerinin yanında parantez içinde eklenmesi:

# AWS SageMaker ve AWS Bedrock Karşılaştırması

Ayrıca, AWS Bedrock gibi daha basit ve daha uygun maliyetli seçenekler yerine neden AWS SageMaker'ı seçtiğimizi tartışmalıyız. İlk olarak, Bedrock ve faydalarını açıklayalım. Amazon Bedrock, Büyük Dil Modelleri (LLMs - Large Language Models) dağıtmak için sunucusuz (serverless) bir çözümdür. Sunucusuz, yönetilecek sunucu veya altyapı olmadığı anlamına gelir. API Çağrıları (API calls) yoluyla doğrudan erişebileceğiniz önceden eğitilmiş modeller (pre-trained models) sağlar. Bu kitabı yazdığımızda, sadece Mistral, Flan, Llama 2 ve Llama 3 için destek sağlıyorlardı (oldukça sınırlı bir seçenek listesi). Giriş Verileri (input data) gönderebilir ve altta yatan altyapı veya yazılımı yönetmeden modellerden Tahminler (predictions) alabilirsiniz. Bu yaklaşım, Yapay Zeka (AI - Artificial Intelligence) yeteneklerini uygulamalara entegre etmek için gereken karmaşıklığı ve zamanı önemli ölçüde azaltır, Makine Öğrenimi (machine learning) konusunda sınırlı uzmanlığa sahip geliştiriciler için daha erişilebilir hale getirir. Ancak, bu entegrasyon kolaylığı, Amazon Bedrock tarafından sağlanan önceden eğitilmiş modellere ve API'lere kısıtlı olduğunuz için sınırlı Özelleştirme Seçenekleri (customization options) maliyetiyle gelir. Fiyatlandırma açısından Bedrock, API Çağrılarının sayısına dayanan basit bir Fiyatlandırma Modeli (pricing model) kullanır. Bu basit fiyatlandırma yapısı, maliyetleri tahmin etmeyi ve kontrol etmeyi daha verimli hale getirir. Bu arada, SageMaker, Makine Öğrenimi modelleri oluşturmak, eğitmek ve dağıtmak için kapsamlı bir platform sağlar. ML süreçlerinizi tamamen özelleştirebilir veya platformu araştırma için kullanabilirsiniz. Bu nedenle SageMaker, programlamayı bilen, Makine Öğrenimi kavramlarını anlayan ve AWS gibi Bulut Platformlarıyla (cloud platforms) çalışmaktan rahat olan Veri Bilimciler (data scientists) ve Makine Öğrenimi uzmanları tarafından 주로 kullanılır. SageMaker, maliyetler açısından çelişkili bir yapıya sahiptir, çoğu AWS hizmetine benzer şekilde kullanılan bir Ödemeye Göre Fiyatlandırma Modeli (pay-as-you-go pricing model) izler. Bu, Hesaplama Kaynaklarının (computing resources), Depolamanın (storage) ve uygulamalarınızı oluşturmak için gereken diğer hizmetlerin kullanımı için ödeme yapmanız gerektiği anlamına gelir. Bedrock'un aksine, SageMaker Uç Noktası (endpoint) kullanılmasa bile, çevrimiçi EC2 Örnekleri (instances) gibi AWS'de dağıtılan kaynaklar için yine de ödeme yaparsınız. Bu nedenle, Kullanılmayan Kaynakları silen Otomatik Ölçeklendirme Sistemleri (autoscaling systems) tasarlamalısınız. Sonuç olarak Bedrock, mevcut Temel Modellerden (foundation models) biriyle desteklenen bir API Uç Noktası (API endpoint) hızlı bir şekilde dağıtmanıza olanak tanıyan kullanıma hazır bir çözüm sunar. Bu arada SageMaker, ML mantığınızı tamamen özelleştirmenizi sağlayan çok işlevli bir platformdur. Peki neden Bedrock yerine SageMaker'ı seçtik? Bedrock, bir şeyleri hızlıca prototiplemek için mükemmel bir çözüm olabilirdi, ancak bu kitap LLM Mühendisliği (LLM engineering) hakkındadır ve amacımız Bedrock'un gizlemeye çalıştığı tüm mühendislik yönlerini incelemektir. Bu nedenle, bir modeli dağıtmak için gereken tüm mühendisliği size gösterebilmemizi sağlayan yüksek düzeyde Özelleştirilebilirlik (customizability) nedeniyle SageMaker'ı seçtik. Gerçekte, SageMaker bile tamamen özelleştirilebilir değildir. Dağıtımınız üzerinde tam kontrol sahibi olmak istiyorsanız, AWS'nin Kubernetes kendi kendine yönetilen hizmeti olan EKS'yi (Elastic Kubernetes Service) kullanın. Bu durumda, Sanal Makinelere (virtual machines) doğrudan erişiminiz olur, böylece ML İşlem Hatlarınızı (pipelines) nasıl oluşturduğunuzu, nasıl etkileşimde bulunduğunu ve Kaynaklarınızı (resources) nasıl yönettiğinizi tamamen özelleştirebilirsiniz. Aynı şeyi AWS ECS (EC2 Container Service) ile de yapabilirsiniz, AWS'nin Kubernetes versiyonu. EKS veya ECS kullanarak maliyetleri de azaltabilirsiniz, çünkü bu hizmetler önemli ölçüde daha az maliyetlidir. Sonuç olarak SageMaker, tam kontrol ve özelleştirme ile tüm mühendislik karmaşıklığını sahne arkasında gizleyen tam olarak yönetilen bir hizmet arasında bir denge kurar. Bu denge, yönetilen hizmetin rahatlığından da yararlanırken ihtiyacınız olan kontrole sahip olmanızı sağlar.

Kodların ayrıntılı olarak satır satır açıklanması:

1. `We must also discuss why we chose AWS SageMaker over simpler and more cost-effective options, such as AWS Bedrock.`
   - Bu cümle, AWS SageMaker'ın neden AWS Bedrock gibi daha basit ve uygun maliyetli seçenekler yerine seçildiğini tartışma ihtiyacını vurgular.

2. `First, let’s explain Bedrock and its benefits.`
   - Bu cümle, önce Bedrock'un ve faydalarının açıklanması gerektiğini belirtir.

3. `Amazon Bedrock is a serverless solution for deploying LLMs.`
   - Amazon Bedrock, Büyük Dil Modellerini (LLMs) dağıtmak için sunucusuz bir çözümdür. "Sunucusuz" terimi, yönetilecek sunucu veya altyapı olmadığı anlamına gelir.

4. `Serverless means that there are no servers or infrastructure to manage.`
   - Sunucusuz, yönetilecek sunucu veya altyapının olmadığını belirtir.

5. `It provides pre-trained models, which you can access directly through API calls.`
   - Bedrock, API çağrıları yoluyla doğrudan erişilebilen önceden eğitilmiş modeller sağlar.

6. `When we wrote this book, they provided support only for Mistral, Flan, Llama 2, and Llama 3 (quite a limited list of options).`
   - Kitap yazıldığında, Bedrock'un sadece Mistral, Flan, Llama 2 ve Llama 3 modellerini desteklediği belirtilir.

7. `You can send input data and receive predictions from the models without managing the underlying infrastructure or software.`
   - Kullanıcı, altta yatan altyapı veya yazılımı yönetmeden modellere giriş verileri gönderebilir ve tahminler alabilir.

8. `This approach significantly reduces the complexity and time required to integrate AI capabilities into applications, making it more accessible to developers with limited machine learning expertise.`
   - Bu yaklaşım, AI yeteneklerini uygulamalara entegre etmek için gereken karmaşıklığı ve zamanı azaltır.

9. `However, this ease of integration comes at the cost of limited customization options, as you’re restricted to the pre-trained models and APIs provided by Amazon Bedrock.`
   - Ancak, entegrasyonun kolaylığı, sınırlı özelleştirme seçenekleri maliyetiyle gelir.

10. `In terms of pricing, Bedrock uses a simple pricing model based on the number of API calls.`
    - Bedrock, API çağrılarının sayısına dayanan basit bir fiyatlandırma modeli kullanır.

11. `This straightforward pricing structure makes it more efficient to estimate and control costs.`
    - Basit fiyatlandırma yapısı, maliyetleri tahmin etmeyi ve kontrol etmeyi daha verimli hale getirir.

12. `Meanwhile, SageMaker provides a comprehensive platform for building, training, and deploying machine learning models.`
    - SageMaker, makine öğrenimi modelleri oluşturmak, eğitmek ve dağıtmak için kapsamlı bir platform sağlar.

13. `It allows you to customize your ML processes entirely or even use the platform for research.`
    - SageMaker, ML süreçlerinizi tamamen özelleştirmenize veya platformu araştırma için kullanmanıza olanak tanır.

14. `That’s why SageMaker is mainly used by data scientists and machine learning experts who know how to program, understand machine learning concepts, and are comfortable working with cloud platforms such as AWS.`
    - SageMaker, esas olarak veri bilimciler ve makine öğrenimi uzmanları tarafından kullanılır.

15. `SageMaker is a double-edged sword regarding costs, following a pay-as-you-go pricing model similar to most AWS services.`
    - SageMaker, maliyetler açısından çelişkili bir yapıya sahiptir, çoğu AWS hizmetine benzer şekilde kullanılan bir ödemeye göre fiyatlandırma modeli izler.

16. `This means you have to pay for the usage of computing resources, storage, and any other services required to build your applications.`
    - Bu, hesaplama kaynaklarının, depolamanın ve uygulamalarınızı oluşturmak için gereken diğer hizmetlerin kullanımı için ödeme yapmanız gerektiği anlamına gelir.

17. `In contrast to Bedrock, even if the SageMaker endpoint is not used, you will still pay for the deployed resources on AWS, such as online EC2 instances.`
    - Bedrock'un aksine, SageMaker uç noktası kullanılmasa bile, AWS'de dağıtılan kaynaklar için yine de ödeme yaparsınız.

18. `Thus, you have to design autoscaling systems that delete unused resources.`
    - Bu nedenle, kullanılmayan kaynakları silen otomatik ölçeklendirme sistemleri tasarlamalısınız.

19. `To conclude, Bedrock offers an out-of-the-box solution that allows you to quickly deploy an API endpoint powered by one of the available foundation models.`
    - Bedrock, mevcut temel modellerden biriyle desteklenen bir API uç noktası hızlı bir şekilde dağıtmanıza olanak tanıyan kullanıma hazır bir çözüm sunar.

20. `Meanwhile, SageMaker is a multi-functional platform enabling you to customize your ML logic fully.`
    - SageMaker, ML mantığınızı tamamen özelleştirmenizi sağlayan çok işlevli bir platformdur.

21. `So why did we choose SageMaker over Bedrock?`
    - Peki neden Bedrock yerine SageMaker'ı seçtik?

22. `Bedrock would have been an excellent solution for quickly prototyping something, but this is a book on LLM engineering, and our goal is to dig into all the engineering aspects that Bedrock tries to mask away.`
    - Bedrock, bir şeyleri hızlıca prototiplemek için mükemmel bir çözüm olabilirdi, ancak bu kitap LLM mühendisliği hakkındadır.

23. `Thus, we chose SageMaker because of its high level of customizability, allowing us to show you all the engineering required to deploy a model.`
    - Bu nedenle, SageMaker'ı yüksek düzeyde özelleştirilebilirlik nedeniyle seçtik.

24. `In reality, even SageMaker isn’t fully customizable.`
    - Gerçekte, SageMaker bile tamamen özelleştirilebilir değildir.

25. `If you want complete control over your deployment, use EKS, AWS’s Kubernetes self-managed service.`
    - Dağıtımınız üzerinde tam kontrol sahibi olmak istiyorsanız, AWS'nin Kubernetes kendi kendine yönetilen hizmeti olan EKS'yi kullanın.

26. `In this case, you have direct access to the virtual machines, allowing you to fully customize how you build your ML pipelines, how they interact, and how you manage your resources.`
    - Bu durumda, sanal makinelere doğrudan erişiminiz olur, böylece ML işlem hatlarınızı nasıl oluşturduğunuzu tamamen özelleştirebilirsiniz.

27. `You could do the same thing with AWS ECS, AWS’s version of Kubernetes.`
    - Aynı şeyi AWS ECS ile de yapabilirsiniz, AWS'nin Kubernetes versiyonu.

28. `Using EKS or ECS, you could also reduce the costs, as these services cost considerably less.`
    - EKS veya ECS kullanarak maliyetleri de azaltabilirsiniz, çünkü bu hizmetler önemli ölçüde daha az maliyetlidir.

29. `To conclude, SageMaker strikes a balance between complete control and customization and a fully managed service that hides all the engineering complexity behind the scenes.`
    - Sonuç olarak SageMaker, tam kontrol ve özelleştirme ile tüm mühendislik karmaşıklığını sahne arkasında gizleyen tam olarak yönetilen bir hizmet arasında bir denge kurar.

30. `This balance ensures that you have the control you need while also benefiting from the managed service’s convenience.`
    - Bu denge, yönetilen hizmetin rahatlığından da yararlanırken ihtiyacınız olan kontrole sahip olmanızı sağlar.

---

