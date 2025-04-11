#Her RAG Sistemi Benzerdir
Her RAG sistemi kökeninde benzerdir. Öncelikle RAG'ı en basit haliyle anlamaya odaklanacağız. Daha sonra, sistemin doğruluğunu artırmak için daha gelişmiş RAG tekniklerini (techniques) aşamalı olarak tanıtacağız. Tekrarı önlemek için vanilla ve naive RAG terimlerini birbirinin yerine kullanacağımızı unutmayın. Bir RAG sistemi, birbirinden bağımsız üç ana modülden (modules) oluşur:
 
Ingestion pipeline (Besleme Boru Hattı): Vektör DB'yi (vector DB) doldurmak için kullanılan bir toplu (batch) veya akış (streaming) boru hattıdır.
Retrieval pipeline (Alma Boru Hattı): Vektör DB'yi sorgulayan ve kullanıcının girdisine (input) ilgili girişleri (entries) alan bir modüldür.
Generation pipeline (Üretim Boru Hattı): Alınan verileri (retrieved data) kullanarak istemi (prompt) zenginleştiren (augment) ve cevaplar üreten bir LLM (Large Language Model) ile birlikte kullanılan bir katmandır (layer).

Bu üç bileşen (components), kendi sınıflarına (classes) veya servislerine (services) sahip olduklarından, her birini ayrı ayrı inceleyeceğiz. Ancak şimdilik, "Bu üç modül nasıl bağlantılı?" sorusunu cevaplamaya çalışalım. İşte çok basit bir genel bakış:
 
Arka uçta (backend side), ingestion pipeline, vektör DB'yi harici verilerle (external data) doldurmak için bir zamanlama (schedule) veya sürekli olarak çalışır.
İstemci tarafında (client side), kullanıcı bir soru sorar. Soru, retrieval module'a (alma modülü) iletilir, bu modül kullanıcının girdisini önceden işler (preprocesses) ve vektör DB'yi sorgular.
Generation pipelines (üretim boru hatları), bir prompt template (istem şablonu), kullanıcı girdisi ve alınan içerik (retrieved context) kullanarak istemi oluşturur. İstem, bir LLM'ye (Large Language Model) iletilir ve cevap üretilir. Cevap kullanıcıya gösterilir.

#Şekil 4.1: Vanilla RAG Mimarisi

Üretken (generative) AI uygulamanızda herhangi bir tür harici bilgiye (external information) erişmeniz gerektiğinde RAG'ı uygulamalısınız. Örneğin, bir finansal asistan uygularken, değerli cevaplar vermeden önce en son haberlere, raporlara ve fiyatlara erişmeniz gerekir. Ya da bir seyahat önerisi (traveling recommender) oluşturuyorsanız, potansiyel turistik yerlerin, restoranların ve etkinliklerin bir listesini almanız ve ayrıştırmalısınız (parse). 

Eğitim zamanında (training time), LLM'ler sizin özel verilerinize (specific data) erişemez, bu nedenle genellikle üretken AI projenizde bir RAG stratejisi (strategy) uygulamak zorunda kalacaksınız. Şimdi, ingestion, retrieval ve generation pipelines'ı ayrıntılı olarak inceleyelim.

### Kodların Ayrıntılı Açıklaması

Yukarıdaki metinde kod bulunmamaktadır. Ancak RAG sisteminin bileşenlerini açıklayan bir metin vardır. Aşağıda basit bir RAG sistemini temsil eden bir kod yapısı örneği verilmiştir:
```python
# Ingestion Pipeline (Besleme Boru Hattı)
class IngestionPipeline:
    def __init__(self, vector_db):
        self.vector_db = vector_db

    def ingest(self, external_data):
        # Harici verileri vektör DB'ye doldurur
        self.vector_db.populate(external_data)

# Retrieval Pipeline (Alma Boru Hattı)
class RetrievalPipeline:
    def __init__(self, vector_db):
        self.vector_db = vector_db

    def retrieve(self, user_input):
        # Kullanıcının girdisine ilgili girişleri vektör DB'den alır
        return self.vector_db.query(user_input)

# Generation Pipeline (Üretim Boru Hattı)
class GenerationPipeline:
    def __init__(self, llm):
        self.llm = llm

    def generate(self, prompt_template, user_input, retrieved_context):
        # İstem şablonunu, kullanıcı girdisini ve alınan içeriği kullanarak istemi oluşturur
        prompt = self.create_prompt(prompt_template, user_input, retrieved_context)
        # İstem'i LLM'ye iletir ve cevap üretir
        return self.llm.generate(prompt)

    def create_prompt(self, prompt_template, user_input, retrieved_context):
        # İstem şablonunu, kullanıcı girdisini ve alınan içeriği kullanarak istemi oluşturur
        # Bu methodun ayrıntıları burada gösterilmemiştir
        pass

# RAG Sistemi
class RAGSystem:
    def __init__(self, ingestion_pipeline, retrieval_pipeline, generation_pipeline):
        self.ingestion_pipeline = ingestion_pipeline
        self.retrieval_pipeline = retrieval_pipeline
        self.generation_pipeline = generation_pipeline

    def run(self, user_input):
        # Kullanıcının girdisini alır ve cevabı üretir
        retrieved_context = self.retrieval_pipeline.retrieve(user_input)
        prompt_template = "Örnek istem şablonu"
        answer = self.generation_pipeline.generate(prompt_template, user_input, retrieved_context)
        return answer

# Örnek kullanım
vector_db = VectorDB()  # Vektör DB örneği
ingestion_pipeline = IngestionPipeline(vector_db)
retrieval_pipeline = RetrievalPipeline(vector_db)
llm = LLM()  # LLM örneği
generation_pipeline = GenerationPipeline(llm)

rag_system = RAGSystem(ingestion_pipeline, retrieval_pipeline, generation_pipeline)

user_input = "Örnek kullanıcı girdisi"
answer = rag_system.run(user_input)
print(answer)
```
Bu kod yapısında, her bir bileşen (ingestion pipeline, retrieval pipeline, generation pipeline) ayrı bir sınıfa (class) sahiptir. RAG sistemi, bu bileşenleri bir araya getirerek çalışır.

---

