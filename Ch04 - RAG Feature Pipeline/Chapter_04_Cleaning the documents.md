# Temizleme Adımı (Cleaning Step)

Temizleme adımında, tüm belgeleri (documents) yineleriz ve tüm mantığı, veri kategorisine (data category) göre hangi temizleme mantığını (cleaning logic) uygulayacağını bilen bir CleaningDispatcher'a devrediyoruz. Hatırlayın ki, makalelere (articles), gönderilere (posts) ve kod depolarına (code repositories) farklı temizleme teknikleri (cleaning techniques) uygulamak veya gelecekte uygulama yeteneğine sahip olmak istiyoruz.

```python
@step
def clean_documents(
    documents: Annotated[list, "raw_documents"],
) -> Annotated[list, "cleaned_documents"]:
    cleaned_documents = []
    for document in documents:
        cleaned_document = CleaningDispatcher.dispatch(document)
        cleaned_documents.append(cleaned_document)
    step_context = get_step_context()
    step_context.add_output_metadata(
        output_name="cleaned_documents", metadata=_get_metadata(cleaned_documents)
    )
    return cleaned_documents
```

**Kod Açıklaması:**

1. `@step`: Bu, bir dekoratördür (decorator) ve fonksiyonun bir adım (step) olarak işaretlendiğini belirtir.
2. `def clean_documents(documents: Annotated[list, "raw_documents"]) -> Annotated[list, "cleaned_documents"]:`
   - Bu fonksiyon `clean_documents` adında bir temizleme işlemi tanımlar.
   - `documents` parametresi, ham belgelerin (raw documents) bir listesini temsil eder ve `Annotated[list, "raw_documents"]` türündedir.
   - Fonksiyonun geri dönüş tipi `Annotated[list, "cleaned_documents"]` olarak belirtilmiştir, yani temizlenmiş belgelerin (cleaned documents) bir listesini döndürür.
3. `cleaned_documents = []`: Temizlenmiş belgeleri saklamak için boş bir liste oluşturur.
4. `for document in documents:` Döngüsü, her bir belgeyi (document) `documents` listesinden yineler.
5. `cleaned_document = CleaningDispatcher.dispatch(document)`: Her belge için, `CleaningDispatcher` sınıfının `dispatch` metodunu çağırarak temizleme işlemini gerçekleştirir. Bu, belgeye uygun temizleme mantığını (cleaning logic) uygular.
6. `cleaned_documents.append(cleaned_document)`: Temizlenmiş belgeyi `cleaned_documents` listesine ekler.
7. `step_context = get_step_context()`: Mevcut adım bağlamını (step context) alır.
8. `step_context.add_output_metadata(output_name="cleaned_documents", metadata=_get_metadata(cleaned_documents))`: Temizlenmiş belgelerle ilgili metadata'yı (örneğin, belgelerin sayısı, boyutları gibi bilgileri) adım bağlamına ekler. `_get_metadata` fonksiyonu bu metadata'yı hesaplar.
9. `return cleaned_documents`: Temizlenmiş belgelerin listesini döndürür.

Hesaplanan metadata, `query_data_warehouse()` adımında kaydettiğimiz bilgilere benzer. Bu nedenle, şimdi parçalama (chunking) ve gömme (embedding) işlemlerine geçelim.

---

