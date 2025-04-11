#Toplu İşlem Hattı (Batch Pipeline)

Toplu işlem hattı, veri sistemlerinde verilerin belirli aralıklarla ve daha büyük hacimlerde toplanması, işlenmesi ve depolanması anlamına gelen bir veri işleme yöntemidir, bu yöntem aynı zamanda "toplu işlem" (batch) olarak da bilinir. Bu yaklaşım, verilerin sürekli olarak geldikçe işlendiği gerçek zamanlı (real-time) veya akışlı (streaming) veri işleme yöntemlerinden farklıdır.

#Toplu İşlem Hattında Gerçekleşenler

Veri toplama (Data collection): Veriler çeşitli kaynaklardan toplanır ve işlenmek üzere yeterli miktarlar biriktirilene kadar depolanır. Bu, veritabanlarından (DBs), günlük kayıtlarından (logs), dosyalardan ve diğer kaynaklardan gelen verileri içerebilir.
```python
# Veri toplama örneği
data_sources = ["DBs", "logs", "files"]
collected_data = []
for source in data_sources:
    # Veri toplama işlemi
    collected_data.extend(collect_data(source))
```

1. `data_sources = ["DBs", "logs", "files"]`: Bu satır, veri kaynaklarını bir liste olarak tanımlar.
2. `collected_data = []`: Bu satır, toplanan verileri depolamak için boş bir liste oluşturur.
3. `for source in data_sources:`: Bu satır, veri kaynakları listesinde döngü oluşturur.
4. `collected_data.extend(collect_data(source))`: Bu satır, her bir veri kaynağından veri toplar ve `collected_data` listesine ekler.

Zamanlanmış işleme (Scheduled processing): Veri işleme, düzenli aralıklarla, örneğin saatlik veya günlük olarak planlanır. Bu süre zarfında, toplanan veriler toplu olarak işlenir. Bu, veri temizleme (data cleansing), dönüştürme (transformation), toplama (aggregation) ve diğer işlemleri içerebilir.
```python
# Zamanlanmış işleme örneği
import schedule
import time

def process_data(data):
    # Veri işleme işlemi
    cleaned_data = clean_data(data)
    transformed_data = transform_data(cleaned_data)
    aggregated_data = aggregate_data(transformed_data)
    return aggregated_data

schedule.every(1).hour.do(process_data, collected_data)  # Her saat başı veri işleme

while True:
    schedule.run_pending()
    time.sleep(1)
```

1. `import schedule` ve `import time`: Bu satırlar, zamanlanmış görevleri yönetmek için `schedule` kütüphanesini ve zaman ile ilgili işlemler için `time` kütüphanesini içe aktarır.
2. `def process_data(data):`: Bu satır, veri işleme işlevini tanımlar.
3. `schedule.every(1).hour.do(process_data, collected_data)`: Bu satır, `process_data` işlevini her saat başı çalıştırmak üzere planlar.
4. `while True:`: Bu satır, sonsuz bir döngü oluşturur.
5. `schedule.run_pending()`: Bu satır, planlanmış görevleri çalıştırır.
6. `time.sleep(1)`: Bu satır, döngüyü 1 saniye boyunca bekletir.

Veri yükleme (Data loading): İşlemden sonra, veriler hedef sisteme, örneğin bir veritabanına (DB), veri ambarına (data warehouse), veri gölüne (data lake) veya özellik deposuna (feature store) yüklenir. Bu işlenmiş veriler daha sonra analiz, sorgulama veya daha ileri işlemler için kullanılabilir.
```python
# Veri yükleme örneği
def load_data(processed_data):
    # Veri yükleme işlemi
    load_into_target_system(processed_data)

load_data(aggregated_data)
```

1. `def load_data(processed_data):`: Bu satır, veri yükleme işlevini tanımlar.
2. `load_into_target_system(processed_data)`: Bu satır, işlenmiş verileri hedef sisteme yükler.

#Toplu İşlem Hatlarının Avantajları

Toplu işlem hatları, büyük hacimli verilerle çalışırken ve anlık işleme gereksinimi olmadığında özellikle yararlıdır. Birkaç avantajı vardır:

*   Verimlilik (Efficiency): Toplu işleme, büyük hacimli verileri gerçek zamanlı işlemeden daha verimli bir şekilde işleyebilir, böylece kaynak tahsisini optimize etmeye ve paralel işleme yapmaya olanak tanır.
*   Karmaşık işleme (Complex processing): Toplu işlem hatları, gerçek zamanlı işleme için çok kaynak yoğun olabilecek karmaşık veri dönüştürmeleri ve toplamaları gerçekleştirebilir.
*   Basitlik (Simplicity): Toplu işleme sistemlerinin mimarileri genellikle gerçek zamanlı sistemlerden daha basittir, bu da onları uygulamasını ve bakımını kolaylaştırır.

---

