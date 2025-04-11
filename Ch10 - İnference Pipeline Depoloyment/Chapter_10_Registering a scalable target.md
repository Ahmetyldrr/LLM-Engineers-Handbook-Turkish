#Kaynaklar için Otomatik Ölçeklendirme (Autoscaling) Etkinleştirmenin İlk Adımı

Kaynaklarınız için otomatik ölçeklendirme (autoscaling) etkinleştirmenin ilk adımı, AWS tarafından sağlanan Uygulama Otomatik Ölçeklendirme (Application Auto Scaling) özelliği ile ölçeklendirilebilir bir hedef (scalable target) kaydetmektir. Bunu, AWS'ye hangi kaynağı ölçeklendirmek istediğinizi ve ölçeklendirmenin hangi sınırlar içinde gerçekleşmesi gerektiğini bildirmek olarak düşünün. Ancak bu adım, ölçeklendirmenin nasıl veya ne zaman gerçekleşmesi gerektiğini belirtmez. Örneğin, SageMaker Çıkarım (Inference) bileşenleri ile çalışırken aşağıdaki tanımları yapacaksınız:
 
 Kaynak Kimliği (Resource ID) : Bu, ölçeklendirmek istediğiniz kaynak için benzersiz bir tanımlayıcı görevi görür ve genellikle SageMaker Çıkarım bileşeninin adını içerir.
 Hizmet Ad Alanı (Service namespace) : Bu, kaynağın ait olduğu AWS hizmetini tanımlar, bu durumda SageMaker'dir.
 Ölçeklendirilebilir Boyut (Scalable dimension) : Bu, istenen kopya sayısı gibi ölçeklendirilecek kaynakları belirtir.
 MinCapacity ve MaxCapacity : Bu parametreler, otomatik ölçeklendirme stratejilerinin sınırlarını tanımlar, örneğin replica sayısının minimum ve maksimum limitleri gibi.
 Ölçeklendirilebilir bir hedef kaydederek, SageMaker Çıkarım bileşeninizin gelecekteki ölçeklendirme eylemleri için hazır hale getirirsiniz, ancak bu eylemlerin ne zaman veya nasıl gerçekleşmesi gerektiğini belirlemezsiniz.

```markdown
The first step in enabling autoscaling for your resources is to register a scalable target with the Application Auto Scaling feature AWS provides. 
Think of this as informing AWS about the specific resource you intend to scale, as well as setting the boundaries within which the scaling should occur. 
However, this step does not dictate how or when the scaling should happen. 
For instance, when working with SageMaker Inference components, you’ll define the following: 
Resource ID : This serves as a unique identifier for the resource you want to scale, typically including the name of the SageMaker Inference component. 
Service namespace : This identifies the AWS service the resource belongs to, which, in this case, is SageMaker . 
Scalable dimension : This specifies the resources to be scaled, such as the desired number of copies. 
MinCapacity and MaxCapacity : These parameters define the boundaries of the autoscaling strategies, such as minimum and maximum limits of the number of replicas. 
By registering a scalable target, you prepare your SageMaker Inference component for future scaling actions without determining when or how these actions should occur.
```

### Kodların Ayrıntılı Açıklaması:

1. **`The first step in enabling autoscaling for your resources is to register a scalable target with the Application Auto Scaling feature AWS provides.`**
   - Kaynaklarınız için otomatik ölçeklendirme (autoscaling) etkinleştirmenin ilk adımı, AWS tarafından sağlanan Uygulama Otomatik Ölçeklendirme (Application Auto Scaling) özelliği ile ölçeklendirilebilir bir hedef (scalable target) kaydetmektir.

2. **`Think of this as informing AWS about the specific resource you intend to scale, as well as setting the boundaries within which the scaling should occur.`**
   - Bunu, AWS'ye hangi kaynağı ölçeklendirmek istediğinizi ve ölçeklendirmenin hangi sınırlar içinde gerçekleşmesi gerektiğini bildirmek olarak düşünün.

3. **`However, this step does not dictate how or when the scaling should happen.`**
   - Ancak bu adım, ölçeklendirmenin nasıl veya ne zaman gerçekleşmesi gerektiğini belirtmez.

4. **`For instance, when working with SageMaker Inference components, you’ll define the following:`**
   - Örneğin, SageMaker Çıkarım (Inference) bileşenleri ile çalışırken aşağıdaki tanımları yapacaksınız.

5. **`Resource ID : This serves as a unique identifier for the resource you want to scale, typically including the name of the SageMaker Inference component.`**
   - Kaynak Kimliği (Resource ID): Bu, ölçeklendirmek istediğiniz kaynak için benzersiz bir tanımlayıcı görevi görür ve genellikle SageMaker Çıkarım bileşeninin adını içerir.

6. **`Service namespace : This identifies the AWS service the resource belongs to, which, in this case, is SageMaker .`**
   - Hizmet Ad Alanı (Service namespace): Bu, kaynağın ait olduğu AWS hizmetini tanımlar, bu durumda SageMaker'dir.

7. **`Scalable dimension : This specifies the resources to be scaled, such as the desired number of copies.`**
   - Ölçeklendirilebilir Boyut (Scalable dimension): Bu, istenen kopya sayısı gibi ölçeklendirilecek kaynakları belirtir.

8. **`MinCapacity and MaxCapacity : These parameters define the boundaries of the autoscaling strategies, such as minimum and maximum limits of the number of replicas.`**
   - MinCapacity ve MaxCapacity: Bu parametreler, otomatik ölçeklendirme stratejilerinin sınırlarını tanımlar, örneğin replica sayısının minimum ve maksimum limitleri gibi.

9. **`By registering a scalable target, you prepare your SageMaker Inference component for future scaling actions without determining when or how these actions should occur.`**
   - Ölçeklendirilebilir bir hedef kaydederek, SageMaker Çıkarım bileşeninizin gelecekteki ölçeklendirme eylemleri için hazır hale getirirsiniz, ancak bu eylemlerin ne zaman veya nasıl gerçekleşmesi gerektiğini belirlemezsiniz.

---

