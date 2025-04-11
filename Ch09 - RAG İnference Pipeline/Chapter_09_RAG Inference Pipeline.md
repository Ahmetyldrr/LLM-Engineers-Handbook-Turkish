# Bölüm Başlığı 
Back in Chapter 4 , we implemented the retrieval-augmented generation ( RAG ) feature pipeline to populate the vector database ( DB ). 
# 4. Bölümde, vektör veritabanını (DB) doldurmak için retrieval-augmented generation (RAG) özellik pipeline'unu uyguladık.

Within the feature pipeline, we gathered data from the data warehouse, cleaned, chunked, and embedded the documents, and, ultimately, loaded them to the vector DB. 
Özellik pipeline'u içinde, veri ambarından (data warehouse) veri topladık, dokümanları temizledik, parçaladık (chunked) ve gömülü (embedded) hale getirdik ve nihayetinde vektör DB'sine yükledik.

Thus, at this point, the vector DB is filled with documents and ready to be used for RAG. 
Böylece, bu noktada, vektör DB dokümanlarla dolu ve RAG için kullanılmaya hazır.

Based on the RAG methodology, you can split your software architecture into three modules: one for retrieval, one to augment the prompt, and one to generate the answer. 
RAG metodolojisine (methodology) göre, yazılım mimarınızı (software architecture) üç modüle ayırabilirsiniz: biri retrieval için, biri prompt'u genişletmek (augment) için ve biri de cevabı üretmek (generate) için.

We will follow a similar pattern by implementing a retrieval module to query the vector DB. 
Benzer bir deseni, vektör DB'sini sorgulamak (query) için bir retrieval modülü uygulayarak takip edeceğiz.

Within this module, we will implement advanced RAG techniques to optimize the search. 
Bu modül içinde, aramayı optimize etmek (optimize) için gelişmiş RAG teknikleri (techniques) uygulayacağız.

Afterward, we won’t dedicate a whole module to augmenting the prompt, as that would be overengineering, which we try to avoid. 
Daha sonra, prompt'u genişletmeye (augmenting) bütün bir modül ayırmayacağız, çünkü bu aşırı mühendislik (overengineering) olur ve bundan kaçınmaya çalışıyoruz.

However, we will write an inference service that inputs the user query and context, builds the prompt, and calls the LLM to generate the answer. 
Ancak, kullanıcı sorgusunu (user query) ve bağlamını (context) input olarak alan, prompt'u oluşturan (builds) ve cevabı üretmek için LLM'i çağıran (calls) bir inference servisi yazacağız.

To summarize, we will implement two core Python modules, one for retrieval and one for calling the LLM using the user’s input and context as input. 
Özetle, retrieval için bir tane ve kullanıcının input'u ve bağlamını input olarak kullanarak LLM'i çağırmak için bir tane olmak üzere iki temel Python modülü uygulayacağız.

When we glue these together, we will have an end-to-end RAG flow. 
Bunları birbirine bağladığımızda (glue), uçtan uca (end-to-end) bir RAG akışı elde edeceğiz.

In Chapters 5 and 6 , we fine-tuned our LLM Twin model, and in Chapter 8 , we learned how to optimize it for inference. 
5. ve 6. Bölümlerde, LLM Twin modelimizi fine-tuned ettik ve 8. Bölümde, bunu inference için optimize etmeyi öğrendik.

Thus, at this point, the LLM is ready for production. 
Böylece, bu noktada, LLM production'a hazır.

What is left is to build and deploy the two modules described above. 
Geriye kalan, yukarıda açıklanan iki modülü inşa etmek (build) ve deploy etmek.

We will dedicate the next chapter entirely to deploying our fine-tuned LLM Twin model to AWS SageMaker, as an AWS SageMaker inference endpoint. 
Sonraki bölümü, fine-tuned LLM Twin modelimizi bir AWS SageMaker inference endpoint'i olarak AWS SageMaker'a deploy etmeye ayıracağız.

Thus, the focus of this chapter is to dig into the advanced RAG retrieval module implementation. 
Böylece, bu bölümün odak noktası, gelişmiş RAG retrieval modülü uygulamasına dalmak (dig into).

We have dedicated a whole chapter to the retrieval step because this is where the magic happens in an RAG system. 
Retrieval adımına bütün bir bölüm ayırdık çünkü RAG sisteminde asıl iş burada gerçekleşiyor.

At the retrieval step (and not when calling the LLM), you write most of the RAG inference code. 
Retrieval adımında (LLM'i çağırırken değil), RAG inference kodunun çoğunu yazarsınız.

This step is where you have to wrangle your data to ensure that you retrieve the most relevant data points from the vector DB. 
Bu adım, vektör DB'sinden en ilgili veri noktalarını (data points) retrieve ettiğinizden emin olmak için verilerinizi wrangle ettiğiniz yerdir.

Hence, most of the advanced RAG logic goes within the retrieval step. 
Dolayısıyla, gelişmiş RAG mantığının (logic) çoğu retrieval adımında yer alır.

To sum up, in this chapter, we will cover the following topics: 
Özetle, bu bölümde aşağıdaki konuları ele alacağız:

# Bu Bölümde İşlenecek Konular
Understanding the LLM Twin’s RAG inference pipeline 
LLM Twin'in RAG inference pipeline'unu anlamak

Exploring the LLM Twin’s advanced RAG techniques 
LLM Twin'in gelişmiş RAG tekniklerini keşfetmek

Implementing the LLM Twin’s RAG inference pipeline 
LLM Twin'in RAG inference pipeline'unu uygulamak

By the end of this chapter, you will know how to implement an advanced RAG retrieval module, augment a prompt using the retrieved context, and call an LLM to generate the final answer. 
Bu bölümün sonunda, gelişmiş bir RAG retrieval modülünü nasıl uygulayacağınızı, retrieved bağlamı (context) kullanarak prompt'u nasıl genişleteceğinizi (augment) ve nihai cevabı üretmek için bir LLM'i nasıl çağıracağınızı bileceksiniz.

Ultimately, you will know how to build a production-ready RAG inference pipeline end to end. 
Nihayetinde, production'a hazır, uçtan uca bir RAG inference pipeline'unu nasıl inşa edeceğinizi bileceksiniz.

### Kodların Ayrıntılı Açıklaması

Bu metinde kod bulunmamaktadır. Ancak, anlatılan konular ve uygulanması gereken adımlar Python kodları ile gerçekleştirilecektir. Aşağıda, anlatılan konulara göre örnek kodlar ve açıklamaları yer almaktadır.

#### Retrieval Modülü Uygulaması
```python
# retrieval_modulu.py
import numpy as np
from sentence_transformers import SentenceTransformer

class RetrievalModule:
    def __init__(self, model_name):
        self.model = SentenceTransformer(model_name)

    def retrieve(self, query, vector_db):
        # Query'i gömülü hale getir
        query_embedding = self.model.encode(query)

        # Vektör DB'sinden en ilgili dokümanları bul
        similarities = np.dot(vector_db, query_embedding)
        most_relevant_docs = np.argsort(-similarities)[:5]

        return most_relevant_docs
```
Bu kod, retrieval modülünü uygulamak için `SentenceTransformer` kütüphanesini kullanmaktadır. `retrieve` methodu, verilen sorguyu gömülü hale getirir ve vektör DB'sinden en ilgili dokümanları bulur.

#### Inference Servisi Uygulaması
```python
# inference_servisi.py
from retrieval_modulu import RetrievalModule
from llm_twin import LLMTwin

class InferenceService:
    def __init__(self, retrieval_module, llm_twin):
        self.retrieval_module = retrieval_module
        self.llm_twin = llm_twin

    def generate_answer(self, user_query, context):
        # Retrieval modülünü kullanarak en ilgili dokümanları bul
        most_relevant_docs = self.retrieval_module.retrieve(user_query, context)

        # Prompt'u oluştur
        prompt = f"{user_query} {most_relevant_docs}"

        # LLM'i çağırarak cevabı üret
        answer = self.llm_twin.generate(prompt)

        return answer
```
Bu kod, inference servisini uygulamak için retrieval modülünü ve LLM Twin modelini kullanmaktadır. `generate_answer` methodu, verilen kullanıcı sorgusunu ve bağlamını kullanarak en ilgili dokümanları bulur, prompt'u oluşturur ve LLM'i çağırarak cevabı üretir.

---

