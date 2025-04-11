# ZenML Pipeline'de ResourceLimitExceeded Hatasının Çözümü

AWS (Amazon Web Services) yığını kullanarak SageMaker'da bir ZenML pipeline çalıştırdıktan sonra ResourceLimitExceeded hatasıyla karşılaştığınızı varsayalım. Bu durumda, AWS'den belirli bir türde AWS EC2 VM (Sanallaştırma Makinesi) erişimi talep etmeniz gerekir. ZenML varsayılan olarak, AWS freemium katmanının bir parçası olan ml.t3.medium EC2 makinelerini kullanır. Ancak, bazı AWS hesapları varsayılan olarak bu VM'lere erişemez.

Erişimizi kontrol etmek için AWS konsolunuzda Service Quotas (Hizmet Kontenjanları) araması yapın. Ardından, sol panelde AWS services (AWS Hizmetleri) üzerine tıklayın, Amazon SageMaker arayın ve ardından ml.t3.medium için arama yapın. Şekil 11.13'te, bu tür makineler için kontenjanlarımızı görebilirsiniz. Eğer sizinki 0 ise, Applied account-level quota value (Uygulanan hesap düzeyinde kontenjan değeri) sütununda Şekil 11.13'tekine benzer sayılar için AWS'den artış talep etmelisiniz. Tüm süreç ücretsizdir ve sadece birkaç tıklama gerektirir. Ne yazık ki, AWS'nin talebinizi kabul etmesini beklemek zorunda kalabilirsiniz; bu süre birkaç saat ila bir gün arasında değişebilir.

# Şekil 11.13: SageMaker—ml.t3.medium Beklenen Kontenjanlar

Bu hatayı çözmek ve yeni kontenjanlar talep etmek için adım adım talimatları aşağıdaki bağlantıda bulabilirsiniz: https://repost.aws/knowledge-center/sagemaker-resource-limit-exceeded-error

`.env` dosyasından değerleri değiştirdiyseniz ve ZenML gizli (secrets) değerlerini güncellemek istiyorsanız, önce eski gizli değerleri silmek için aşağıdaki CLI komutunu çalıştırın:

```bash
poetry poe delete-settings-zenml
```

Ardından, aşağıdaki komutu çalıştırarak tekrar export edebilirsiniz:

```bash
poetry poe export-settings-to-zenml
```

### Kodların Ayrıntılı Açıklaması

1. **`poetry poe delete-settings-zenml`**
   - Bu komut, ZenML için ayarlanan gizli (secret) değerleri silmek için kullanılır.
   - `poetry poe` komutu, `pyproject.toml` içinde tanımlanmış özel komutları çalıştırmak için kullanılır.
   - `delete-settings-zenml` komutu, ZenML ile ilgili ayarları silmek için özel olarak tanımlanmış bir komuttur.

2. **`poetry poe export-settings-to-zenml`**
   - Bu komut, `.env` dosyasındaki değerleri ZenML gizli değerlerine export etmek için kullanılır.
   - Yine `poetry poe` aracılığıyla çalıştırılır ve `export-settings-to-zenml` komutu, ZenML için ayarları export etmek üzere tanımlanmıştır.

Her iki komut da `pyproject.toml` içinde `[tool.poe.tasks]` altında tanımlanmalıdır. Örneğin:

```toml
[tool.poe.tasks]
delete-settings-zenml = "zenml delete-settings-zenml"
export-settings-to-zenml = "zenml export-settings-to-zenml"
```

Bu tanımlar, ZenML ile ilgili işlemleri kolaylaştırmak için özel komutlar sağlar.

---

