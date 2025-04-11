# RAG Sisteminin Son Adımı
RAG sisteminin son adımı, kullanıcının girdisini almak, verileri geri çekmek, bir LLM'e (Large Language Model) iletmek ve değerli bir cevap oluşturmaktır. Nihai istem (prompt), kullanıcının sorgusu ve geri alınan içerik ile doldurulan bir sistem ve istem şablonundan (prompt template) kaynaklanır. Uygulamanıza bağlı olarak tek bir istem şablonunuz veya birden fazla istem şablonunuz olabilir. Genellikle, tüm istem mühendisliği (prompt engineering), istem şablonu düzeyinde yapılır. Aşağıda, genel bir sistem ve istem şablonunun nasıl göründüğüne ve geri alma mantığı (retrieval logic) ve LLM ile birlikte nihai cevabı oluşturmak için nasıl kullanıldıklarına dair bir örnek görebilirsiniz:

```python
system_template = """ 
You are a helpful assistant who answers all the user's questions politely.
"""

prompt_template = """ 
Answer the user's question using only the provided context. If you cannot answer using the context, respond with "I don't know."
Context: {context}
User question: {user_question}
"""

user_question = "<your_question>"
retrieved_context = retrieve(user_question)

prompt = f"{system_template} \n"
prompt += prompt_template.format(context=retrieved_context, user_question=user_question)

answer = llm(prompt)
```

**Kod Açıklaması:**

1. `system_template = """ You are a helpful assistant who answers all the user's questions politely. """` 
   - Bu satır, sistemin genel davranışını tanımlayan bir şablonu tanımlar. Bu şablon, asistanın kullanıcı sorularını kibarca cevaplaması gerektiğini belirtir.

2. `prompt_template = """ Answer the user's question using only the provided context. If you cannot answer using the context, respond with "I don't know." Context: {context} User question: {user_question} """`
   - Bu satır, asıl istem şablonunu tanımlar. Bu şablon, kullanıcının sorusunu yalnızca sağlanan içerik (context) kullanarak cevaplamayı denemek için kullanılır. İçerik yetersizse, "I don't know." (Bilmiyorum) cevabı verilir.

3. `user_question = "<your_question>"` 
   - Bu satır, kullanıcının sorusunu temsil eder. `<your_question>` kısmı gerçek bir soru ile değiştirilmelidir.

4. `retrieved_context = retrieve(user_question)`
   - Bu satır, kullanıcının sorusuna göre ilgili içerik (context) geri alma işlemini yapar. `retrieve` fonksiyonu, kullanıcının sorusunu alır ve ilgili içerikleri döndürür.

5. `prompt = f"{system_template} \n"` 
   - Bu satır, nihai istemi oluşturmaya başlar. Sistem şablonunu (`system_template`) içerir.

6. `prompt += prompt_template.format(context=retrieved_context, user_question=user_question)`
   - Bu satır, istem şablonunu (`prompt_template`), geri alınan içerik (`retrieved_context`) ve kullanıcının sorusunu (`user_question`) kullanarak biçimlendirir ve nihai isteme ekler.

7. `answer = llm(prompt)`
   - Bu satır, oluşturulan nihai istemi (`prompt`), bir Büyük Dil Modeli'ne (LLM - Large Language Model) geçirir ve bir cevap (`answer`) alır.

İstem şablonları geliştikçe, her değişiklik makine öğrenimi operasyonları (MLOps - Machine Learning Operations) en iyi uygulamaları kullanılarak izlenmeli ve sürümlenmelidir. Böylece, eğitim veya çıkarım (inference) zamanında, belirli bir cevabın belirli bir LLM ve istem şablonları sürümü tarafından üretildiğini her zaman bilirsiniz. Bunu Git aracılığıyla yapabilir, istem şablonlarını bir veritabanında (DB) saklayabilir veya LangFuse gibi belirli istem yönetimi araçlarını kullanabilirsiniz.

Geri alma işlem hattında (retrieval pipeline) gördüğümüz gibi, RAG sisteminizin doğruluğunu doğrudan etkileyen bazı kritik yönler, genellikle vektör veritabanlarında (vector DBs) depolanan harici verilerin embeddings (gömme) leri, kullanıcının sorgusunun embedding'i ve kosinüs mesafesi (cosine distance) gibi fonksiyonları kullanarak ikisi arasındaki benzerlikleri nasıl bulabileceğimizdir. RAG algoritmasının bu kısmını daha iyi anlamak için, gömme (embeddings) lerin ne olduğuna ve nasıl hesaplandıklarına bakalım.

**İngilizce Teknik Terimler Parantez İçinde:**

- RAG (Retrieval-Augmented Generation) sistemi
- LLM (Large Language Model)
- MLOps (Machine Learning Operations)
- DB (Database)
- Embeddings (Gömme)
- Vektör DBs (Vector Databases)
- Kosinüs mesafesi (Cosine Distance)

---

