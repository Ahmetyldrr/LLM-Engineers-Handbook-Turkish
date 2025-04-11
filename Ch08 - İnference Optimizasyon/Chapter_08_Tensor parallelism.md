#Tensor Paralelizmi (TP)

Shoeby, Patwary, Puri ve diğerleri tarafından Megatron-LM makalesinde (2019) tanıtılan tensor paralelizmi (TP), LLM katmanlarının hesaplanmasını birden fazla cihaz arasında dağıtmak için kullanılan bir başka popüler tekniktir. Boru hattı paralelizmine (pipeline parallelism) karşıt olarak, TP, bireysel katmanlarda bulunan ağırlık matrislerini (weight matrices) böler. Bu, eşzamanlı hesaplamaları mümkün kılar, bellek darboğazlarını (memory bottlenecks) önemli ölçüde azaltır ve işlem hızını artırır.

TP'de, MLP'lerdeki ağırlık matrisleri (weight matrices) veya kendi kendine dikkat katmanlarındaki (self-attention layers) dikkat başlıkları (attention heads) gibi büyük matrisler, birkaç GPU'ya bölünür. Her GPU, bu matrislerin bir bölümünü tutar ve kendi dilimini işler. 
Örneğin, bir MLP katmanında, ağırlık matrisi, her GPU'nun yalnızca ağırlıkların bir alt kümesini işleyeceği şekilde bölünür (bkz. Şekil 8.7). Girdiler tüm GPU'lara yayınlanır (broadcast) ve daha sonra bağımsız olarak kendi çıktılarını hesaplarlar. Kısmi sonuçlar daha sonra bir tüm-indirgeme (all-reduce) işlemi yoluyla toplanır ve birleştirilerek nihai çıktı oluşturulur.

Kendi kendine dikkat katmanları (self-attention layers) bağlamında, TP, dikkat başlıklarının (attention heads) doğal paralelizminden dolayı özellikle verimlidir. Her GPU, bu başlıkların bir alt kümesini bağımsız olarak hesaplayabilir, böylece modelin büyük dizileri daha etkili bir şekilde işlemesine olanak tanır. Bu, önceki katmanların tamamlanmasını beklemeyi gerektiren boru hattı paralelizminden (pipeline parallelism) daha verimli hale getirir.

Faydalı olmasına rağmen, TP, bir sinir ağının tüm katmanlarına evrensel olarak uygulanamaz. Tüm girdi boyunca bağımlılıkları olan LayerNorm ve Dropout gibi katmanlar, verimli bir şekilde bölünemez ve genellikle cihazlar arasında çoğaltılır. Ancak, bu işlemler girdi dizisinin boyutu (sequence dimension) üzerinde bölünebilir (dizi paralelizmi - sequence parallelism). Farklı GPU'lar, girdi dizisinin farklı dilimlerinde bu katmanları hesaplayabilir, böylece ağırlıkların çoğaltılmasını önler. Bu teknik, birkaç özel katmanla sınırlıdır, ancak özellikle çok büyük girdi dizisi uzunlukları için ek bellek tasarrufu sağlayabilir.

Ayrıca, TP, iletişim yükünü (communication overhead) en aza indirmek için cihazlar arasında yüksek hızlı ara bağlantılar (high-speed interconnects) gerektirir, bu da yetersiz ara bağlantı bant genişliği olan düğümler arasında uygulanmasını pratik hale getirmez.

TP, Megatron-LM, DeepSpeed (ZeRO) ve PyTorch (FSDP) gibi dağıtık eğitim çerçevelerinde (distributed training frameworks) uygulanmıştır. TGI, vLLM ve TensorRT-LLM gibi çoğu çıkarım çerçevesinde (inference frameworks) de mevcuttur.

 Kodların ayrıntılı açıklaması:

*   `Introduced by Shoeby, Patwary, Puri et al. in the Megatron-LM paper (2019)`: Bu kısım, Tensor Paralelizmi (TP) tekniğinin Megatron-LM makalesinde Shoeby, Patwary, Puri ve diğerleri tarafından tanıtıldığını belirtir.
*   `tensor parallelism ( TP ) is another popular technique to distribute the computation of LLM layers across multiple devices`: Bu cümle, TP'nin LLM katmanlarının hesaplanmasını birden fazla cihaz arasında dağıtmak için kullanılan bir teknik olduğunu açıklar.
*   `In contrast to pipeline parallelism, TP splits the weight matrices found in individual layers`: Bu kısım, TP'nin boru hattı paralelizmine (pipeline parallelism) karşıt olarak, bireysel katmanlardaki ağırlık matrislerini (weight matrices) böldüğünü belirtir.
*   `This enables simultaneous computations, significantly reducing memory bottlenecks and increasing processing speed`: Bu cümle, TP'nin eşzamanlı hesaplamaları mümkün kıldığını, bellek darboğazlarını (memory bottlenecks) önemli ölçüde azalttığını ve işlem hızını artırdığını açıklar.
*   `In TP, large matrices, such as the weight matrices in MLPs or the attention heads in self-attention layers, are partitioned across several GPUs`: Bu kısım, TP'de büyük matrislerin (MLP'lerdeki ağırlık matrisleri veya kendi kendine dikkat katmanlarındaki dikkat başlıkları gibi) birkaç GPU'ya bölündüğünü belirtir.
*   `Each GPU holds a portion of these matrices and performs computations on its respective slice`: Bu cümle, her GPU'nun bu matrislerin bir bölümünü tuttuğunu ve kendi dilimini işlediğini açıklar.
*   `The inputs are broadcast to all GPUs, which then independently compute their respective outputs`: Bu kısım, girdilerin tüm GPU'lara yayınlandığını (broadcast) ve daha sonra bağımsız olarak kendi çıktılarını hesapladıklarını belirtir.
*   `The partial results are then aggregated through an all-reduce operation, combining them to form the final output`: Bu cümle, kısmi sonuçların bir tüm-indirgeme (all-reduce) işlemi yoluyla toplandığını ve birleştirilerek nihai çıktı oluşturulduğunu açıklar.

İngilizce Teknik Terimlerin Türkçe Karşılıkları Parantez İçinde Eklenmiştir:

#Tensor Paralelizmi (TP)

Shoeby, Patwary, Puri ve diğerleri tarafından Megatron-LM makalesinde (2019) tanıtılan tensor paralelizmi (TP), LLM katmanlarının hesaplanmasını (computation of LLM layers) birden fazla cihaz arasında dağıtmak için kullanılan bir başka popüler tekniktir (technique). Boru hattı paralelizmine (pipeline parallelism) karşıt olarak, TP, bireysel katmanlarda bulunan ağırlık matrislerini (weight matrices) böler. Bu, eşzamanlı hesaplamaları (simultaneous computations) mümkün kılar, bellek darboğazlarını (memory bottlenecks) önemli ölçüde azaltır ve işlem hızını (processing speed) artırır.

TP'de, MLP'lerdeki ağırlık matrisleri (weight matrices in MLPs) veya kendi kendine dikkat katmanlarındaki (self-attention layers) dikkat başlıkları (attention heads) gibi büyük matrisler (large matrices), birkaç GPU'ya bölünür (partitioned across several GPUs). Her GPU, bu matrislerin bir bölümünü (a portion of these matrices) tutar ve kendi dilimini (respective slice) işler. 
Örneğin, bir MLP katmanında (MLP layer), ağırlık matrisi (weight matrix), her GPU'nun yalnızca ağırlıkların bir alt kümesini (a subset of the weights) işleyeceği şekilde bölünür (divided). Girdiler tüm GPU'lara yayınlanır (broadcast to all GPUs) ve daha sonra bağımsız olarak kendi çıktılarını (respective outputs) hesaplarlar. Kısmi sonuçlar (partial results) daha sonra bir tüm-indirgeme (all-reduce operation) işlemi yoluyla toplanır (aggregated) ve birleştirilerek nihai çıktı (final output) oluşturulur.

Kendi kendine dikkat katmanları (self-attention layers) bağlamında, TP, dikkat başlıklarının (attention heads) doğal paralelizminden (inherent parallelism) dolayı özellikle verimlidir (efficient). Her GPU, bu başlıkların bir alt kümesini (a subset of these heads) bağımsız olarak hesaplayabilir (compute independently), böylece modelin büyük dizileri (large sequences) daha etkili bir şekilde işlemesine (process more effectively) olanak tanır. Bu, önceki katmanların tamamlanmasını (completion of previous layers) beklemeyi gerektiren boru hattı paralelizminden (pipeline parallelism) daha verimli hale getirir.

Faydalı olmasına rağmen, TP, bir sinir ağının (neural network) tüm katmanlarına (all layers) evrensel olarak uygulanamaz (universally applicable). Tüm girdi boyunca (entire input) bağımlılıkları (dependencies) olan LayerNorm ve Dropout gibi katmanlar (layers), verimli bir şekilde bölünemez (efficiently partitioned) ve genellikle cihazlar arasında çoğaltılır (replicated across devices). Ancak, bu işlemler girdi dizisinin boyutu (sequence dimension of the input) üzerinde bölünebilir (split). Farklı GPU'lar, girdi dizisinin farklı dilimlerinde (different slices of the input sequence) bu katmanları hesaplayabilir (compute these layers), böylece ağırlıkların çoğaltılmasını (replication of weights) önler. Bu teknik, birkaç özel katmanla (a few specific layers) sınırlıdır, ancak özellikle çok büyük girdi dizisi uzunlukları (very large input sequence lengths) için ek bellek tasarrufu (additional memory savings) sağlayabilir.

Ayrıca, TP, iletişim yükünü (communication overhead) en aza indirmek için cihazlar arasında yüksek hızlı ara bağlantılar (high-speed interconnects) gerektirir, bu da yetersiz ara bağlantı bant genişliği (insufficient interconnect bandwidth) olan düğümler arasında uygulanmasını pratik hale getirmez.

TP, Megatron-LM, DeepSpeed (ZeRO) ve PyTorch (FSDP) gibi dağıtık eğitim çerçevelerinde (distributed training frameworks) uygulanmıştır. TGI, vLLM ve TensorRT-LLM gibi çoğu çıkarım çerçevesinde (inference frameworks) de mevcuttur.

---

