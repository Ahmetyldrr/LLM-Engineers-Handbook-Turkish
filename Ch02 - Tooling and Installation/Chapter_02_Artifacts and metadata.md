# ZenML'de Yapıt (Artifact) Kavramı ve Kullanımı

Önceki bölümde de belirtildiği gibi, ZenML herhangi bir adım çıktısını bir yapıt (artifact) haline getirir. İlk olarak, bir yapıtın ne olduğunu hızlıca anlayalım. MLOps'ta (Machine Learning Operations), bir yapıt makine öğrenimi yaşam döngüsü sırasında üretilen herhangi bir dosya(lar)dır, örneğin veri kümeleri (datasets), eğitilmiş modeller (trained models), kontrol noktaları (checkpoints) veya günlükler (logs). Yapıtlar deneyleri yeniden üretmek ve modelleri dağıtmak için çok önemlidir. Herhangi bir şeyi bir yapıt haline getirebiliriz. Örneğin, model kaydı (model registry), bir yapıt için özel bir kullanım durumudur. Böylece, yapıtların şu benzersiz özellikleri vardır: sürümlendirilmiş (versioned), paylaşılabilir (sharable) ve içinde ne olduğunu hızlıca anlamak için meta verileri (metadata) onlara eklenmiştir. Örneğin, veri kümenizi bir yapıt ile sarmaladığınızda, meta verilerine veri kümesinin boyutunu, eğitim-test bölme oranını (train-test split ratio), etiketlerin boyutunu ve türlerini ve veri kümesinin içinde ne olduğunu gerçekten indirmeden anlamak için yararlı olan diğer her şeyi ekleyebilirsiniz.

# Digital_data_etl Pipeline Örneği

Hadi `digital_data_etl` pipeline örneğimize geri dönelim, burada bir adım çıktısı olarak bir yapıt, yani taranmış bağlantılar (crawled links) elde ettik, ki bu da bir yapıttır, Şekil 2.7'de görüldüğü gibi.

Şekil 2.7: ZenML yapıt örneği, `digital_data_etl` pipeline kullanılarak

Taranmış bağlantılar (crawled_links) yapıtına tıklayarak ve Meta Veriler (Metadata) sekmesine giderek, belirli bir yazar için taradığımız tüm alanları, her alan için taradığımız bağlantı sayısını ve kaç tanesinin başarılı olduğunu hızlıca görebiliriz, Şekil 2.8'de gösterildiği gibi:

Şekil 2.8: ZenML meta veri örneği, `digital_data_etl` pipeline kullanılarak

Daha ilginç bir yapıt ve meta veri örneği, oluşturulan veri kümesi (dataset) yapıtıdır. Şekil 2.9'da, `instruct_datasets` yapıtının meta verilerini görselleştirebiliriz, ki bu otomatik olarak oluşturulmuştur ve LLM Twin modelini fine-tune etmek için kullanılacaktır. Talimat veri kümeleri (instruction datasets) hakkında daha fazla ayrıntı Bölüm 5'te verilmiştir. Şimdilik, veri kümesinin meta verilerinde, kaç veri kategorisi içerdiği, depolama boyutu ve eğitim ve test bölme başına örnek sayısı gibi birçok yararlı bilgiyi önceden hesapladığımızı vurgulamak istiyoruz.

Şekil 2.9: `instruct_datasets` yapıtı için ZenML meta veri örneği

Meta veriler, aşağıdaki kod snippet'inde gösterildiği gibi, yapıta manuel olarak eklenir. Böylece, işiniz ve projeleriniz genelinde veri kümesi keşfi için yararlı olduğunu düşündüğünüz her şeyi önceden hesaplayabilir ve yapıtın meta verilerine ekleyebilirsiniz:

```python
# Daha fazla import
from zenml import ArtifactConfig, get_step_context, step

@step
def generate_instruction_dataset(
    prompts: Annotated[dict[DataCategory, list[GenerateDatasetSamplesPrompt]], "prompts"]
) -> Annotated[
    InstructTrainTestSplit,
    ArtifactConfig(
        name="instruct_datasets",
        tags=["dataset", "instruct", "cleaned"],
    ),
]:
    datasets = ...  # Veri kümelerini oluştur
    step_context = get_step_context()
    step_context.add_output_metadata(output_name="instruct_datasets", metadata=_get_metadata_instruct_dataset(datasets))
    return datasets

def _get_metadata_instruct_dataset(datasets: InstructTrainTestSplit) -> dict[str, Any]:
    instruct_dataset_categories = list(datasets.train.keys())
    train_num_samples = {
        category: instruct_dataset.num_samples for category, instruct_dataset in datasets.train.items()
    }
    test_num_samples = {category: instruct_dataset.num_samples for category, instruct_dataset in datasets.test.items()}
    return {
        "data_categories": instruct_dataset_categories,
        "test_split_size": datasets.test_split_size,
        "train_num_samples_per_category": train_num_samples,
        "test_num_samples_per_category": test_num_samples,
    }
```

Kod Açıklaması:

1. `generate_instruction_dataset` adlı bir adım (step) tanımlanmıştır. Bu adım, `prompts` adlı bir girdi alır ve `InstructTrainTestSplit` türünde bir çıktı verir.
2. Çıktı, `ArtifactConfig` ile yapılandırılmıştır, burada `name` "instruct_datasets" olarak belirlenmiştir ve etiketler (tags) olarak "dataset", "instruct" ve "cleaned" atanmıştır.
3. Adım içinde, `datasets` oluşturulur ve `step_context` elde edilir.
4. `step_context.add_output_metadata` çağrısı ile çıktıya meta veriler eklenir. Meta veriler, `_get_metadata_instruct_dataset` fonksiyonu tarafından oluşturulur.
5. `_get_metadata_instruct_dataset` fonksiyonu, veri kümesinin kategorilerini, test bölme boyutunu ve eğitim ve test bölme başına örnek sayısını içeren bir sözlük döndürür.

Ayrıca, Evrensel Benzersiz Tanımlayıcı (UUID - Universally Unique Identifier) kullanarak belirli bir sürümdeki veri kümesini kolayca indirebilir ve erişebilirsiniz. UUID'yi ZenML kontrol paneli veya CLI kullanarak bulabilirsiniz:

```python
from zenml.client import Client

artifact = Client().get_artifact_version('8bba35c4-8ff9-4d8f-a039-08046efc9fdc')
loaded_artifact = artifact.load()
```

Kod Açıklaması:

1. `zenml.client` modülünden `Client` sınıfı import edilir.
2. `Client` örneği oluşturulur ve `get_artifact_version` metodu çağrılır, UUID '8bba35c4-8ff9-4d8f-a039-08046efc9fdc' olan yapıt sürümünü almak için.
3. Elde edilen yapıt, `load` metodu ile yüklenir.

ZenML'i keşfetmenin son adımı, bir ZenML pipeline'ı nasıl çalıştıracağınızı ve yapılandıracağınızı anlamaktır.

---

