# LoRA: Parametre-Etkin İnce Ayar Tekniği (Parameter-Efficient Fine-Tuning Technique)

LoRA, büyük dil modellerinin (LLM - Large Language Models) ince ayarını yapmak için parametre-etkin bir tekniktir. Muazzam sinir ağlarını (neural networks) uyarlamayla ilgili hesaplama zorluklarını ele almak için geliştirilen LoRA, kısa sürede LLM ince ayarının temel bir tekniği haline geldi. LoRA'nın birincil amacı, büyük dil modellerinin ince ayarını önemli ölçüde azaltılmış hesaplama kaynaklarıyla etkinleştirmektir. Bu, orijinal parametreleri değiştirmeden modelin davranışını değiştiren eğitilebilir düşük ranklı matrisler (trainable low-rank matrices) tanıtılarak elde edilir.

LoRA'nın temel avantajları şunlardır:
- Eğitim sırasında dramatik olarak azaltılmış bellek kullanımı (memory usage)
- Daha hızlı ince ayar süreci (faster fine-tuning process)
- Önceden eğitilmiş model ağırlıklarının korunması (preservation of pre-trained model weights - non-destructive)
- LoRA ağırlıklarını değiştirerek görevler arasında etkin bir şekilde geçiş yapabilme yeteneği

Bu avantajlar, LoRA'yı sınırlı hesaplama kaynaklarıyla çalışan araştırmacılar ve geliştiriciler için özellikle çekici kılmıştır ve LLM ince ayar sürecini etkin bir şekilde demokratikleştirmiştir.

# LoRA'nın Çalışma Prensibi

LoRA, temel olarak, model ağırlıklarını etkin bir şekilde güncellemek için düşük ranklı bir ayrıştırma tekniği (low-rank decomposition technique) kullanır. Orijinal ağırlık matrisini (weight matrix) doğrudan değiştirmek yerine, LoRA iki daha küçük matris, $A$ ve $B$, tanıtır; bunlar birlikte $W$'ye düşük ranklı bir güncelleme oluşturur. Şekil 5.10 – LoRA, eğitilebilir iki matris $A$ ve $B$'yi ekler ve önceden eğitilmiş ağırlıkları dondurur (freezes).

Matematiksel olarak bu, şu şekilde temsil edilebilir:
$W' = W + AB$

Burada, $W$ orijinal ağırlık matrisidir, $A$ ve $B$ LoRA matrisleridir ve $W'$ çıkarım sırasında kullanılan etkin ağırlık matrisidir (effective weight matrix). $A$ ve $B$ matrislerinin boyutları, çarpımlarının $W$ ile aynı şekle sahip olacak şekilde, ancak çok daha düşük bir rankla seçilir. Bu rank, genellikle $r$ olarak gösterilir, LoRA'da önemli bir hiperparametredir (hyperparameter).

Eğitim sırasında, orijinal ağırlıklar dondurulurken, yalnızca $A$ ve $B$ güncellenir. Bu yaklaşım, eğitilebilir parametre sayısını önemli ölçüde azaltarak, önemli ölçüde bellek tasarrufu ve daha hızlı eğitim süreleri sağlar.

# LoRA'nın Uygulanması

LoRA'yı etkin bir şekilde uygulamak için, doğru hiperparametreleri ve hedef modülleri seçmemiz gerekir. LoRA iki hiperparametre ile gelir:
- Rank ($r$): LoRA matrislerinin boyutunu belirler. Ortak bir başlangıç noktası $r = 8$'dir, ancak bazı durumlarda 256'ya kadar değerler iyi sonuçlar göstermiştir. Daha büyük ranklar daha çeşitli görevleri yakalayabilir, ancak aşırı uyuma (overfitting) yol açabilir.
- Alpha ($\alpha$): LoRA güncellemesine uygulanan bir ölçekleme faktörüdür (scaling factor). Uygulamada, dondurulmuş ağırlıkları $\alpha/r$ faktörüyle güncelleriz. Bu nedenle, ortak bir sezgisel yaklaşım, $\alpha$'yi $r$'nin iki katına eşit olarak ayarlamaktır, LoRA güncellemesine etkin bir şekilde 2 ölçekleme faktörü uygular.

Ayrıca, aşırı uyumu önlemek için bir dropout katmanı eklemek mümkündür. Dropout oranı genellikle isteğe bağlı bir düzenleme faktörü olarak 0 ile 0.1 arasında ayarlanır, bu da eğitim hızını biraz azaltır.

LoRA, model mimarisinin çeşitli bölümlerine uygulanabilir. Başlangıçta, LoRA öncelikle dikkat mekanizmasını (attention mechanism), özellikle sorgu ($Q$) ve değer ($V$) matrislerini transformer katmanlarında değiştirmeye odaklanmıştır. Ancak deneyler, LoRA'nın uygulamasını modelin diğer önemli bileşenlerine genişletmenin önemli faydalar gösterdiğini kanıtlamıştır. Bu ek hedef modülleri şunları içerir:
- Dikkat katmanlarındaki anahtar ($K$) matrisleri
- Dikkat mekanizmalarında çıktı projeksiyon katmanları (genellikle $O$ olarak gösterilir)
- Dikkat katmanları arasındaki beslemeli ileri veya Çok Katmanlı Algılayıcı (MLP - Multi-Layer Perceptron) blokları
- Doğrusal çıktı katmanları

Bununla birlikte, LoRA ile uyarlanan modüllerin sayısının artırılmasının aynı zamanda eğitilebilir parametre sayısını ve dolayısıyla bellek gereksinimlerini de artırdığını belirtmek önemlidir.

LoRA kullanarak, belirli bir yapılandırmaya bağlı olarak, tek bir GPU üzerinde 14-18 GB VRAM ile 7B parametre modelini ince ayarlamak mümkündür. Bu, tipik olarak birden fazla üst düzey GPU gerektiren tam ince ayara kıyasla dramatik bir azalmadır.

Eğitilebilir parametreler açısından, LoRA tam ince ayara kıyasla sayıyı önemli ölçüde azaltır. Örneğin, $r = 16$ rankıyla her modülü hedeflerken bile, bir Llama 3 8B modeli yalnızca 42 milyon eğitilebilir LoRA parametresi içerir, yani modelin parametrelerinin %0.5196'sı.

Kalite açısından, LoRA aynı zamanda tam ince ayarla karşılaştırılabilir veya bazen daha iyi sonuçlar elde edebilir. Birden fazla LoRA ağırlığı seti farklı görevler veya alanlar için birleştirilebilir, böylece yeniden eğitim olmadan esnek dağıtım ve görev değiştirme sağlanır. LoRAX gibi farklı projeler, çoklu LoRA sunumunda uzmanlaşmıştır. Ayrıca, Hugging Face'in Metin Üretimi Çıkarımı (TGI - Text Generation Inference) ve Nvidia Çıkarım Mikroservisleri (NIM - Nvidia Inference Microservices) tarafından desteklenen bir özelliktir.

### Kod Açıklaması

LoRA'nın uygulanması aşağıdaki kod satırlarıyla gösterilebilir:
```python
import torch
import torch.nn as nn

class LoRA(nn.Module):
    def __init__(self, original_layer, rank, alpha):
        super(LoRA, self).__init__()
        self.original_layer = original_layer
        self.rank = rank
        self.alpha = alpha
        
        # LoRA matrislerini başlat
        self.lora_A = nn.Parameter(torch.zeros(original_layer.weight.size(0), rank))
        self.lora_B = nn.Parameter(torch.zeros(rank, original_layer.weight.size(1)))
        
        # Ağırlıkları sıfırla başlat
        nn.init.zeros_(self.lora_A)
        nn.init.normal_(self.lora_B)
        
    def forward(self, x):
        # Orijinal ağırlıkları dondur
        original_output = self.original_layer(x)
        
        # LoRA güncellemesini hesapla
        lora_update = self.alpha / self.rank * (self.lora_A @ self.lora_B)
        
        # LoRA ile güncellenmiş ağırlıkları uygula
        output = original_output + lora_update @ x
        
        return output

# Örnek kullanım
if __name__ == "__main__":
    # Orijinal katmanı tanımla
    original_layer = nn.Linear(512, 512)
    
    # LoRA'yı uygula
    lora_layer = LoRA(original_layer, rank=8, alpha=16)
    
    # Giriş tensörü oluştur
    input_tensor = torch.randn(1, 512)
    
    # İleri geçişi gerçekleştir
    output = lora_layer(input_tensor)
    
    print(output.shape)
```

1. `LoRA` sınıfı tanımlanır ve `nn.Module` sınıfından türetilir.
2. `__init__` metodu, orijinal katmanı, rank'ı ve alpha'yı alır ve LoRA matrislerini başlatır.
3. `forward` metodu, orijinal katmanın çıktısını hesaplar, LoRA güncellemesini uygular ve güncellenmiş çıktıyı döndürür.
4. Örnek kullanımda, orijinal bir doğrusal katman tanımlanır, LoRA uygulanır ve ileri geçiş gerçekleştirilir.

Bu kod, LoRA'nın temel prensiplerini ve nasıl uygulanacağını gösterir. Gerçek dünya uygulamalarında, daha fazla detay ve farklı yapılandırmalar gerekebilir.

---

