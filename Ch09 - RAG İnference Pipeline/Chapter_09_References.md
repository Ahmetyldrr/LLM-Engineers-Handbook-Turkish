# Kaynakça (References)

Aşağıdaki ingilizce paragrafın bire bir türkçe çevirisi yapılmıştır.

A real-time retrieval system for social media data | VectorHub by SuperLinked . (n.d.). https://superlinked.com/vectorhub/articles/real-time-retrieval-system-social-media-data 
Building a Router from Scratch - LlamaIndex . (n.d.). https://docs.llamaindex.ai/en/stable/examples/low_level/router/ 
How to add memory to chatbots | LangChain . (n.d.). https://python.langchain.com/docs/how_to/chatbots_memory/#summary-memory 
How to do “self-querying” retrieval | LangChain . (n.d.). https://python.langchain.com/docs/how_to/self_query/ 
How to route between sub-chains | LangChain . (n.d.). https://python.langchain.com/docs/how_to/routing/#routing-by-semantic-similarity 
How to use the MultiQueryRetriever | LangChain . (n.d.). https://python.langchain.com/docs/how_to/MultiQueryRetriever/ 
Hybrid Search explained . (2023, January 3). Weaviate. https://weaviate.io/blog/hybrid-search-explained 
Iusztin, P. (2024, August 20). 4 Advanced RAG Algorithms You Must Know | Decoding ML. Medium . https://medium.com/decodingml/the-4-advanced-rag-algorithms-you-must-know-to-implement-5d0c7f1199d2 
Monigatti, L. (2024, February 19). Advanced Retrieval-Augmented Generation: From Theory to LlamaIndex Implementation. Medium . https://towardsdatascience.com/advanced-retrieval-augmented-generation-from-theory-to-llamaindex-implementation-4de1464a9930 
Multi-attribute search with vector embeddings | VectorHub by Superlinked . (n.d.). https://superlinked.com/vectorhub/articles/multi-attribute-semantic-search 
Optimizing RAG with Hybrid Search & Reranking | VectorHub by Superlinked . (n.d.). https://superlinked.com/vectorhub/articles/optimizing-rag-with-hybrid-search-reranking 
Refactoring.Guru. (2024, January 1). Singleton . https://refactoring.guru/design-patterns/singleton 
Stoll, M. (2024, September 7). Visualize your RAG Data—Evaluate your Retrieval-Augmented Generation System with Ragas. Medium . https://towardsdatascience.com/visualize-your-rag-data-evaluate-your-retrieval-augmented-generation-system-with-ragas-fc2486308557 
Using LLM’s for retrieval and reranking—LlamaIndex, data framework for LLM applications . (n.d.). https://www.llamaindex.ai/blog/using-llms-for-retrieval-and-reranking-23cf2d3a14b6

# Türkçe Çeviri

Sosyal medya verileri için gerçek zamanlı bir erişim sistemi (Real-time retrieval system for social media data) | SuperLinked tarafından VectorHub . (tarih yok). https://superlinked.com/vectorhub/articles/real-time-retrieval-system-social-media-data 
Sıfırdan bir Yönlendirici (Router) oluşturma - LlamaIndex . (tarih yok). https://docs.llamaindex.ai/en/stable/examples/low_level/router/ 
Chatbotlara (Chatbots) hafıza (memory) nasıl eklenir | LangChain . (tarih yok). https://python.langchain.com/docs/how_to/chatbots_memory/#summary-memory 
“Kendi sorgusunu yapma” (self-querying) erişimi nasıl yapılır | LangChain . (tarih yok). https://python.langchain.com/docs/how_to/self_query/ 
Alt zincirler (sub-chains) arasında yönlendirme (routing) nasıl yapılır | LangChain . (tarih yok). https://python.langchain.com/docs/how_to/routing/#routing-by-semantic-similarity 
MultiQueryRetriever nasıl kullanılır | LangChain . (tarih yok). https://python.langchain.com/docs/how_to/MultiQueryRetriever/ 
Hibrit Arama (Hybrid Search) açıklanıyor . (2023, Ocak 3). Weaviate. https://weaviate.io/blog/hybrid-search-explained 
Iusztin, P. (2024, Ağustos 20). Bilmeniz Gereken 4 Gelişmiş RAG Algoritması | Decoding ML. Medium . https://medium.com/decodingml/the-4-advanced-rag-algorithms-you-must-know-to-implement-5d0c7f1199d2 
Monigatti, L. (2024, Şubat 19). Gelişmiş Erişim-Geliştirilmiş Üretim (Retrieval-Augmented Generation): Teoriden LlamaIndex Uygulamasına. Medium . https://towardsdatascience.com/advanced-retrieval-augmented-generation-from-theory-to-llamaindex-implementation-4de1464a9930 
Vektör gömmeleri (vector embeddings) ile çoklu özellik araması (Multi-attribute search) | Superlinked tarafından VectorHub . (tarih yok). https://superlinked.com/vectorhub/articles/multi-attribute-semantic-search 
Hibrit Arama (Hybrid Search) ve Yeniden Sıralama (Reranking) ile RAG'ı optimize etme | Superlinked tarafından VectorHub . (tarih yok). https://superlinked.com/vectorhub/articles/optimizing-rag-with-hybrid-search-reranking 
Refactoring.Guru. (2024, Ocak 1). Singleton . https://refactoring.guru/design-patterns/singleton 
Stoll, M. (2024, Eylül 7). RAG Verilerinizi Görselleştirin—Ragas ile Erişim-Geliştirilmiş Üretim Sisteminizi Değerlendirin. Medium . https://towardsdatascience.com/visualize-your-rag-data-evaluate-your-retrieval-augmented-generation-system-with-ragas-fc2486308557 
Erişim ve yeniden sıralama için LLM’lerin kullanılması—LlamaIndex, LLM uygulamaları için veri çerçevesi . (tarih yok). https://www.llamaindex.ai/blog/using-llms-for-retrieval-and-reranking-23cf2d3a14b6

# Kodların Açıklaması

Bu kaynakçada kodlar bulunmamaktadır. Ancak, bağlantılarda verilen sayfalarda kod örnekleri bulunabilir.

Örneğin, LangChain belgelerinde (`https://python.langchain.com/docs/how_to/chatbots_memory/#summary-memory`) chatbotslara hafıza eklemek için kullanılan kod örnekleri Python programlama dilinde verilmiştir.

```python
# Örnek kod
from langchain import LLMChain, PromptTemplate
from langchain.memory import ConversationBufferMemory

# Hafıza oluşturma
memory = ConversationBufferMemory()

# Zincir oluşturma
chain = LLMChain(
    llm=LLM, 
    prompt=PromptTemplate(template="...", input_variables=["..."]),
    memory=memory
)
```

Bu kodda, `ConversationBufferMemory` sınıfı kullanılarak bir hafıza oluşturulur ve `LLMChain` sınıfı kullanılarak bir zincir oluşturulur. Zincir, bir LLM (Large Language Model), bir PromptTemplate ve hafıza içerir.

Benzer şekilde, LlamaIndex belgelerinde (`https://docs.llamaindex.ai/en/stable/examples/low_level/router/`) yönlendirici oluşturmak için kullanılan kod örnekleri de Python programlama dilinde verilmiştir.

```python
# Örnek kod
from llama_index import Router

# Yönlendirici oluşturma
router = Router()

# Yönlendirme kuralları tanımlama
router.add_route(
    path="/...",
    handler=...
)
```

Bu kodda, `Router` sınıfı kullanılarak bir yönlendirici oluşturulur ve yönlendirme kuralları tanımlanır.

Bu kod örnekleri, verilen bağlantılardaki sayfalarda daha ayrıntılı olarak açıklanmıştır.

---

