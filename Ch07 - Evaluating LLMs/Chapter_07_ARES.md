# ARES Değerlendirme Çerçevesi (ARES Evaluation Framework)

ARES (RAG sistemleri için otomatik değerlendirme çerçevesi (Automated Evaluation Framework for RAG Systems)) RAG sistemlerini değerlendirmek için tasarlanmış kapsamlı bir araçtır. Sentetik veri üretimi (Synthetic Data Generation) ile ince ayarlı sınıflandırıcıları (Fine-Tuned Classifiers) birleştirerek RAG performansı (RAG Performance) çeşitli yönlerini değerlendiren otomatik bir süreç sunar. ARES çerçevesi üç ana aşamada çalışır: sentetik veri üretimi, sınıflandırıcı eğitimi (Classifier Training) ve RAG değerlendirmesi (RAG Evaluation). Her aşama yapılandırılabilir (Configurable), kullanıcıların değerlendirme sürecini özel ihtiyaçlarına ve veri kümelerine göre uyarlamalarına olanak tanır.

## Sentetik Veri Üretimi (Synthetic Data Generation)
Sentetik veri üretimi aşamasında, ARES gerçek dünya senaryolarını yakından taklit eden veri kümeleri oluşturur (Dataset Generation). Kullanıcılar belge dosya yollarını (Document File Paths), few-shot istem dosyalarını (Few-Shot Prompt Files) ve sentetik sorgular için çıktı konumlarını belirleyerek bu süreci yapılandırabilir. Çerçeve, bu görev için çeşitli önceden eğitilmiş dil modellerini (Pre-Trained Language Models) destekler ve varsayılan olarak google/flan-t5-xxl kullanılır. Kullanıcılar, örneklenen belge sayısını ve diğer parametreleri kapsamlı kapsama ile hesaplama verimliliği arasında denge kurmak için kontrol edebilir.

### Kod:
```python
# Sentetik veri üretimi için örnek kod
ares.generate_synthetic_data(
    document_file_paths=['path/to/documents'],
    few_shot_prompt_file='path/to/few_shot_prompt.json',
    output_location='path/to/output'
)
```
### Açıklama:
- `document_file_paths`: Belge dosyalarının yollarını içeren liste.
- `few_shot_prompt_file`: Few-shot istem dosyasının yolu.
- `output_location`: Sentetik sorguların çıktı konumu.

## Sınıflandırıcı Eğitimi (Classifier Training)
Sınıflandırıcı eğitimi aşamasında, RAG çıktılarının (RAG Outputs) alaka düzeyini ve sadakatini belirlemek için yüksek hassasiyetli sınıflandırıcılar oluşturulur. Kullanıcılar sınıflandırma veri kümesini (Classification Dataset), değerlendirme için test kümesini (Test Set), etiket sütunlarını (Label Columns) ve model seçimini belirleyebilir. ARES, varsayılan olarak microsoft/deberta-v3-large modelini kullanır, ancak diğer Hugging Face modellerini destekler. Eğitim parametreleri, sınıflandırıcı performansını optimize etmek için ince ayar yapılabilir.

### Kod:
```python
# Sınıflandırıcı eğitimi için örnek kod
ares.train_classifier(
    classification_dataset='path/to/classification_dataset.csv',
    test_set='path/to/test_set.csv',
    label_columns=['label'],
    model_name='microsoft/deberta-v3-large'
)
```
### Açıklama:
- `classification_dataset`: Sınıflandırma veri kümesinin yolu.
- `test_set`: Değerlendirme için test kümesinin yolu.
- `label_columns`: Etiket sütunlarının listesi.
- `model_name`: Kullanılacak modelin adı.

## RAG Değerlendirmesi (RAG Evaluation)
RAG değerlendirme aşamasında, eğitilmiş sınıflandırıcılar ve sentetik veriler kullanılarak RAG modelinin performansı değerlendirilir. Kullanıcılar değerlendirme veri kümelerini, değerlendirme için few-shot örneklerini, sınıflandırıcı kontrol noktalarını (Classifier Checkpoints) ve altın etiket yollarını (Gold Label Paths) sağlar. ARES, çeşitli değerlendirme metriklerini (Evaluation Metrics) destekler ve değerlendirmeleri için güven aralıkları (Confidence Intervals) oluşturabilir.

### Kod:
```python
# RAG değerlendirmesi için örnek kod
ares.evaluate_rag(
    evaluation_datasets=['path/to/evaluation_dataset.csv'],
    few_shot_examples='path/to/few_shot_examples.json',
    classifier_checkpoints='path/to/classifier_checkpoint.pth',
    gold_label_paths='path/to/gold_labels.csv'
)
```
### Açıklama:
- `evaluation_datasets`: Değerlendirme veri kümelerinin listesi.
- `few_shot_examples`: Few-shot örneklerinin yolu.
- `classifier_checkpoints`: Sınıflandırıcı kontrol noktalarının yolu.
- `gold_label_paths`: Altın etiketlerin yolu.

ARES, esnek model yürütme seçenekleri sunar ve hem bulut tabanlı hem de yerel çalıştırma işlemlerini vLLM entegrasyonu (vLLM Integration) aracılığıyla destekler. Çerçeve, çeşitli eser türlerini (Artifact Types) destekler ve farklı RAG sistemi çıktılarına göre kapsamlı değerlendirme sağlar.

Ragas ve ARES, değerlendirme ve veri kümesi oluşturma yaklaşımları aracılığıyla birbirlerini tamamlar. Ragas'ın üretim izleme (Production Monitoring) ve LLM destekli metriklerdeki (LLM-Assisted Metrics) gücü, ARES'ın son derece yapılandırılabilir değerlendirme süreci ve sınıflandırıcı tabanlı değerlendirmeleri ile birleştirilebilir. Ragas, LLM yeteneklerine dayalı daha incelikli değerlendirmeler sunabilirken, ARES sınıflandırıcıları eğitildikten sonra tutarlı ve potansiyel olarak daha hızlı değerlendirmeler sağlar. Bunları birleştirmek, Ragas ile hızlı yinelemelerden ve ARES ile derinlemesine, özelleştirilmiş değerlendirmelerden yararlanarak kapsamlı bir değerlendirme çerçevesi sunar. Bir sonraki bölümde, görev spesifik TwinLlama-3.1-8B modelimizi değerlendirmek için kendi değerlendirme çerçevemizi oluşturacağız.

---

