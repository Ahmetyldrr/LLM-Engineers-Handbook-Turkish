# Tüm ZenML Pipeline'ları Çalıştırma

Tüm ZenML pipeline'ları, GitHub deposunda `tools/run.py` konumunda bulunan `run.py` dosyasından çağrılabilir. `run.py` dosyasında, hangi pipeline'ın çalıştırılacağını belirtmenize olanak tanıyan basit bir CLI (Command-Line Interface) uyguladık. Örneğin, Maxime'nin içeriğini crawl etmek için `digital_data_etl` pipeline'ını çağırmak üzere aşağıdaki komutu çalıştırmanız gerekir:
```bash
python -m tools.run --run-etl --no-cache --etl-config-filename digital_data_etl_maxime_labonne.yaml
```
Veya Paul'un içeriğini crawl etmek için:
```bash
python -m tools.run --run-etl --no-cache --etl-config-filename digital_data_etl_paul_iusztin.yaml
```
Poe the Poet'i tanıtırken de açıklandığı gibi, projeyle etkileşimde bulunmak için kullandığımız tüm CLI komutları, projeyi basitleştirmek ve standardize etmek için Poe aracılığıyla yürütülür. Bu nedenle, bu Python çağrılarını aşağıdaki poe CLI komutları altında kapsülledik:
```bash
poetry poe run-digital-data-etl-maxime
poetry poe run-digital-data-etl-paul
```
Farklı kişiler için içerik kazarken yalnızca ETL yapılandırma dosyası adını değiştiriyoruz.

### ETL Yapılandırma Dosyası

ZenML, belirli yapılandırma dosyalarını çalışma zamanında aşağıdaki gibi enjekte etmemize olanak tanır:
```python
config_path = root_dir / "configs" / etl_config_filename
assert config_path.exists(), f"Config file not found: {config_path}"
run_args_etl = {
    "config_path": config_path,
    "run_name": f"digital_data_etl_run_{dt.now().strftime('%Y_%m_%d_%H_%M_%S')}"
}
digital_data_etl.with_options(**run_args_etl)
```
Yapılandırma dosyasında, pipeline'a girdi olarak parametreler belirtiyoruz. Örneğin, `configs/digital_data_etl_maxime_labonne.yaml` yapılandırma dosyası aşağıdaki gibidir:
```yml
parameters:
  user_full_name: Maxime Labonne # [First Name(s)] [Last Name]
  links:
    # Personal Blog
    - https://mlabonne.github.io/blog/posts/2024-07-29_Finetune_Llama31.html
    - https://mlabonne.github.io/blog/posts/2024-07-15_The_Rise_of_Agentic_Data_Generation.html
    # Substack
    - https://maximelabonne.substack.com/p/uncensor-any-llm-with-abliteration-d30148b7d43e
    … # More links
```
`digital_data_etl` fonksiyon imzası aşağıdaki gibidir:
```python
@pipeline
def digital_data_etl(user_full_name: str, links: list[str]) -> str:
```
Bu yaklaşım, kodu değiştirmeden her pipeline'ı çalışma zamanında yapılandırmamıza olanak tanır. Ayrıca, tüm pipeline'larımızın girdilerini açıkça takip edebilir ve yeniden üretilebilirliği sağlayabiliriz.

### Diğer Orchestrator'lar

ZenML'ye benzer diğer popüler orchestrator'lar arasında Airflow, Prefect, Metaflow ve Dagster bulunur. Ayrıca, Kubernetes'in yoğun kullanıcıları için Agro Workflows veya Kubeflow'u tercih edebilirsiniz, ancak Kubeflow yalnızca Kubernetes üzerinde çalışır. Biz, ZenML'nin kullanım kolaylığı, özellikleri ve maliyetleri arasında en iyi dengeyi sunduğunu düşünüyoruz. Ayrıca, bu araçların hiçbiri ZenML'nin sunduğu stack özelliğini sunmaz, bu da sizi herhangi bir bulut ekosistemine kilitlememesini sağlar.

### Kod Açıklaması

1. `config_path = root_dir / "configs" / etl_config_filename`:
   - `root_dir`: Proje kök dizinini temsil eder.
   - `configs`: Yapılandırma dosyalarının bulunduğu dizin.
   - `etl_config_filename`: ETL yapılandırma dosyasının adı.
   - Bu satır, ETL yapılandırma dosyasının tam yolunu oluşturur.

2. `assert config_path.exists(), f"Config file not found: {config_path}"`:
   - `config_path.exists()`: Belirtilen yapılandırma dosyasının var olup olmadığını kontrol eder.
   - Eğer dosya yoksa, hata mesajı fırlatır.

3. `run_args_etl = {...}`:
   - `config_path`: ETL yapılandırma dosyasının yolu.
   - `run_name`: Pipeline'ın çalıştırma adını oluşturur, bu ad tarih ve saat içerir.
   - Bu sözlük, `digital_data_etl` pipeline'ına geçirilecek argümanları içerir.

4. `digital_data_etl.with_options(**run_args_etl)`:
   - `digital_data_etl`: Pipeline fonksiyonu.
   - `with_options()`: Pipeline'a ek seçenekler geçirmek için kullanılır.
   - `**run_args_etl`: `run_args_etl` sözlüğünü pipeline'a argüman olarak geçirir.

5. `@pipeline def digital_data_etl(user_full_name: str, links: list[str]) -> str:`:
   - `@pipeline`: Fonksiyonun bir pipeline olduğunu belirtir.
   - `user_full_name` ve `links`: Pipeline'ın girdi parametreleri.
   - `-> str`: Pipeline'ın döndürdüğü değerin tipi.

Bu kodlar, ZenML pipeline'larının nasıl çalıştırıldığını, yapılandırıldığını ve diğer orchestrator'larla karşılaştırıldığını açıklamaktadır.

---

