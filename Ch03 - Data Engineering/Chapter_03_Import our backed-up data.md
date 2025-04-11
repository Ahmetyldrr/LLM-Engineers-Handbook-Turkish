#Hiçbir Şey İşe Yaramazsa
If nothing works, there is the possibility of populating the MongoDB (MongoDB) database with your backed-up data saved under the `data/data_warehouse_raw_data` directory . 
Eğer hiçbir şey işe yaramazsa, MongoDB (MongoDB) veritabanını `data/data_warehouse_raw_data` dizininde kayıtlı yedeklenmiş verilerle doldurma imkanı vardır.

This will allow you to proceed to the fine-tuning (ince ayar) and inference (çıkarım) sections without running the data collection ETL (ETL - Extract, Transform, Load) code. 
Bu, veri toplama ETL (ETL - Extract, Transform, Load) kodunu çalıştırmadan ince ayar (fine-tuning) ve çıkarım (inference) bölümlerine geçmenize olanak tanır.

To import all the data within this directory, run: 
Bu dizindeki tüm verileri içe aktarmak için çalıştırın:

```bash
poetry poe run-import-data-warehouse-from-json
```

**Kod Açıklaması:**
- `poetry`: Poetry, Python projeleri için bağımlılık yönetimi ve paketleme aracıdır. 
- `poe`: Poe, Poetry ile birlikte kullanılan bir görev çalıştırma aracıdır. 
- `run-import-data-warehouse-from-json`: Bu, Poe ile tanımlanmış özel bir görevdir (task). Bu görev, `data/data_warehouse_raw_data` dizinindeki JSON verilerini MongoDB veritabanına içe aktarır.

After running the CLI (Komut Satırı Arayüzü) command from above, you will have a one-to-one replica ( birebir kopya) of the dataset we used while developing the code. 
Yukarıdaki CLI (Komut Satırı Arayüzü) komutunu çalıştırdıktan sonra, kod geliştirme aşamasında kullandığımız veri setinin birebir kopyasına (one-to-one replica) sahip olacaksınız.

To ensure the import is completed successfully, you should have 88 articles and 3 users in your MongoDB (MongoDB) database.
İçe aktarma işleminin başarıyla tamamlandığından emin olmak için MongoDB (MongoDB) veritabanınızda 88 makale ve 3 kullanıcı olmalıdır.

---

