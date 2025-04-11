# Ölçeklenebilir Hedefin Kaydedilmesinden Ölçeklendirme Politikası Oluşturmaya

Ölçeklenebilir hedefinizi kaydettikten sonra, bir sonraki adım ölçeklendirmenin nasıl gerçekleşeceğini tanımlamaktır. Bu, bir ölçeklendirme politikası (Scaling Policy) oluşturmanın geldiği yerdir. Bir ölçeklendirme politikası, ölçeklendirme olaylarını tetikleyen belirli kurallar tanımlar. Politikalar oluştururken, izlenecek metriği (metric) ve ölçeklendirme olaylarını ne zaman yayacağını belirlemek için eşikleri (thresholds) tanımlamanız gerekir. SageMaker Inference bileşenimiz bağlamında, ölçeklenebilir politika aşağıdaki unsurları içerebilir: 
Politika türü (Policy Type): Örneğin, izlenen bir metrik için belirli bir hedef değeri korumak üzere kaynağın kapasitesini ayarlayan TargetTrackingScaling politikasını seçebilirsiniz. 
Hedef izleme yapılandırması (Target Tracking Configuration): Bu, izlenecek metriği (SageMakerInferenceComponentInvocationsPerCopy gibi) seçmeyi, istenen hedef değeri ayarlamayı ve önceki ölçeklendirme eylemlerinden sonra ölçeklendirme eylemlerinin ne kadar hızlı gerçekleşebileceğini kontrol eden soğuma sürelerini (cooldown periods) belirtmeyi içerir. 
Ölçeklendirme politikası, ölçeklendirme-içi (scaling-in) ve ölçeklendirme-dışı (scaling-out) stratejinizi tanımlar. Belirtilen metriği sürekli olarak izler ve metrik hedef değeri aşarsa veya altına düşerse, kayıtlı ölçeklenebilir hedef tarafından tanımlanan sınırlar içinde her zaman çıkarılan sonuç bileşeni kopya sayısını ölçeklendirmek için eylemler tetikler.

# TargetTrackingScaling Politikasının Çalışması

TargetTrackingScaling politikanın nasıl çalıştığını daha derinlemesine açıklayalım. Uygulamanız için ideal ortalama kullanım veya verim düzeyini temsil eden bir metriğiniz olduğunu düşünün. Hedef izleme ile bu metriği seçer ve uygulamanız için en uygun durumu yansıtan bir hedef değer belirlersiniz. Tanımlandıktan sonra, Application Auto Scaling gerekli CloudWatch alarmalarını oluşturur ve yönetir. Sapmalar olduğunda, ölçeklendirme eylemleri tetiklenir, tıpkı bir termostatın tutarlı bir oda sıcaklığını korumak için ayarlanması gibi. 
Örneğin, SageMaker üzerinde çalışan bir uygulama düşünün. GPU kullanımını yaklaşık %70 civarında tutmayı hedeflediğimizi varsayalım. Bu hedef, ani trafik artışlarını yönetmek için yeterli boşluk bırakırken, boşta kalan kaynakların gereksiz maliyetini önlemeye de yardımcı olur. GPU kullanımı hedefi aştığında, sistem ölçeklendirme yaparak artan yükü yönetmek için kaynak ekler. Tersine, GPU kullanımı hedef altına düştüğünde, sistem daha sessiz dönemlerde maliyetleri en aza indirmek için kapasiteyi azaltarak ölçeklendirir.

# Uygulama Otomatik Ölçeklendirmenin Avantajları

Application Auto Scaling kullanarak hedef izleme politikaları kurmanın önemli bir avantajı, ölçeklendirme sürecini basitleştirmesidir. Artık CloudWatch alarmalarını yapılandırmaya ve ölçeklendirme ayarlarını manuel olarak tanımlamaya gerek kalmaz.

**Kod Açıklaması:**

Paragrafta kod bulunmamaktadır, ancak ilgili kavramları açıklayan bir metin vardır. Ancak, ölçeklendirme politikası oluşturmak için kullanılan bazı teknik terimleri açıklayan kod benzeri bir yapı aşağıdaki gibidir:

*   `Policy Type`: Politika Türü
*   `TargetTrackingScaling`: Hedef İzleme Ölçeklendirmesi
*   `Target Tracking Configuration`: Hedef İzleme Yapılandırması
*   `SageMakerInferenceComponentInvocationsPerCopy`: Kopya Başına SageMaker Çıkarım Bileşeni Çağrıları
*   `Cooldown Periods`: Soğuma Süreleri

Bu terimler, ölçeklendirme politikası oluştururken kullanılan önemli kavramlardır.

İngilizce Teknik Terimlerin Türkçeleri Parantez içinde Eklenmiştir:

# Ölçeklenebilir Hedefin (Scalable Target) Kaydedilmesinden Ölçeklendirme Politikası (Scaling Policy) Oluşturmaya

Ölçeklenebilir hedefinizi (Scalable Target) kaydettikten sonra, bir sonraki adım ölçeklendirmenin (Scaling) nasıl gerçekleşeceğini tanımlamaktır. Bu, bir ölçeklendirme politikası (Scaling Policy) oluşturmanın geldiği yerdir. Bir ölçeklendirme politikası, ölçeklendirme olaylarını (Scaling Events) tetikleyen belirli kurallar tanımlar. Politikalar (Policies) oluştururken, izlenecek metriği (Metric) ve ölçeklendirme olaylarını ne zaman yayacağını belirlemek için eşikleri (Thresholds) tanımlamanız gerekir. SageMaker Inference bileşenimiz bağlamında, ölçeklenebilir politika aşağıdaki unsurları içerebilir: 
Politika türü (Policy Type): Örneğin, izlenen bir metrik için belirli bir hedef değeri korumak üzere kaynağın kapasitesini ayarlayan TargetTrackingScaling politikasını seçebilirsiniz. 
Hedef izleme yapılandırması (Target Tracking Configuration): Bu, izlenecek metriği (SageMakerInferenceComponentInvocationsPerCopy gibi) seçmeyi, istenen hedef değeri ayarlamayı ve önceki ölçeklendirme eylemlerinden sonra ölçeklendirme eylemlerinin ne kadar hızlı gerçekleşebileceğini kontrol eden soğuma sürelerini (Cooldown Periods) belirtmeyi içerir. 
Ölçeklendirme politikası, ölçeklendirme-içi (Scaling-in) ve ölçeklendirme-dışı (Scaling-out) stratejinizi tanımlar. Belirtilen metriği sürekli olarak izler ve metrik hedef değeri aşarsa veya altına düşerse, kayıtlı ölçeklenebilir hedef (Scalable Target) tarafından tanımlanan sınırlar içinde her zaman çıkarılan sonuç bileşeni kopya sayısını ölçeklendirmek için eylemler tetikler.

# TargetTrackingScaling Politikasının Çalışması

TargetTrackingScaling politikanın nasıl çalıştığını daha derinlemesine açıklayalım. Uygulamanız için ideal ortalama kullanım veya verim düzeyini temsil eden bir metriğiniz olduğunu düşünün. Hedef izleme (Target Tracking) ile bu metriği seçer ve uygulamanız için en uygun durumu yansıtan bir hedef değer belirlersiniz. Tanımlandıktan sonra, Application Auto Scaling gerekli CloudWatch alarmalarını oluşturur ve yönetir. Sapmalar olduğunda, ölçeklendirme eylemleri tetiklenir, tıpkı bir termostatın tutarlı bir oda sıcaklığını korumak için ayarlanması gibi. 
Örneğin, SageMaker üzerinde çalışan bir uygulama düşünün. GPU kullanımını yaklaşık %70 civarında tutmayı hedeflediğimizi varsayalım. Bu hedef, ani trafik artışlarını yönetmek için yeterli boşluk bırakırken, boşta kalan kaynakların gereksiz maliyetini önlemeye de yardımcı olur. GPU kullanımı hedefi aştığında, sistem ölçeklendirme yaparak artan yükü yönetmek için kaynak ekler. Tersine, GPU kullanımı hedef altına düştüğünde, sistem daha sessiz dönemlerde maliyetleri en aza indirmek için kapasiteyi azaltarak ölçeklendirir.

# Uygulama Otomatik Ölçeklendirmenin (Application Auto Scaling) Avantajları

Application Auto Scaling kullanarak hedef izleme politikaları kurmanın önemli bir avantajı, ölçeklendirme sürecini basitleştirmesidir. Artık CloudWatch alarmalarını yapılandırmaya ve ölçeklendirme ayarlarını manuel olarak tanımlamaya gerek kalmaz.

---

