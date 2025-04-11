#ZenML Pipeline ve Veri Toplama İşlemleri

2. Bölümde sunduğumuz gibi, LLM Twin projemizdeki her bir pipeline'ın giriş noktası (entry point), YAML dosyaları aracılığıyla çalışma zamanında (runtime) yapılandırılabilen ve ZenML ekosistemi üzerinden çalıştırılabilen bir ZenML pipeline'dır. Bu nedenle, ZenML `digital_data_etl` pipeline'ına bakalım. Bunun, ZenML'i örneklendirmek için 2. Bölümde kullandığımız aynı pipeline olduğunu fark edeceksiniz. Ancak bu sefer, veri toplama işleminin arka planda nasıl çalıştığını açıklayarak implementasyonuna daha derinlemesine ineceğiz. Pipeline'ın nasıl çalıştığını anladıktan sonra, çeşitli sitelerden veri toplamak için kullanılan her bir crawler'ın implementasyonunu ve veri ambarından (data warehouse) veri depolamak ve sorgulamak için kullanılan MongoDB belgelerini (documents) inceleyeceğiz.

İngilizce paragraf:
```
As we presented in Chapter 2 , the entry point to each pipeline from our LLM Twin project is a ZenML pipeline, which can be configured at runtime through YAML files and run through the ZenML ecosystem. Thus, let’s start by looking into the ZenML digital_data_etl pipeline. You’ll notice that this is the same pipeline we used as an example in Chapter 2 to illustrate ZenML. But this time, we will dig deeper into the implementation, explaining how the data collection works behind the scenes. After understanding how the pipeline works, we will explore the implementation of each crawler used to collect data from various sites and the MongoDB documents used to store and query data from the data warehouse.
```

Kodların ayrıntılı açıklaması:
```
As we presented in Chapter 2 , 
# 2. Bölümde sunduğumuz gibi

the entry point to each pipeline from our LLM Twin project 
# LLM Twin projemizdeki her bir pipeline'ın giriş noktası (entry point)

is a ZenML pipeline, 
# bir ZenML pipeline'ıdır

which can be configured at runtime 
# çalışma zamanında (runtime) yapılandırılabilen

through YAML files 
# YAML dosyaları aracılığıyla

and run through the ZenML ecosystem. 
# ve ZenML ekosistemi üzerinden çalıştırılabilen

Thus, let’s start by looking into the ZenML digital_data_etl pipeline. 
# Bu nedenle, ZenML `digital_data_etl` pipeline'ına bakalım.

You’ll notice that this is the same pipeline we used as an example in Chapter 2 to illustrate ZenML. 
# Bunun, ZenML'i örneklendirmek için 2. Bölümde kullandığımız aynı pipeline olduğunu fark edeceksiniz.

But this time, we will dig deeper into the implementation, 
# Ancak bu sefer, implementasyonuna daha derinlemesine ineceğiz

explaining how the data collection works behind the scenes. 
# veri toplama işleminin arka planda nasıl çalıştığını açıklayarak

After understanding how the pipeline works, 
# Pipeline'ın nasıl çalıştığını anladıktan sonra

we will explore the implementation of each crawler used to collect data from various sites 
# çeşitli sitelerden veri toplamak için kullanılan her bir crawler'ın implementasyonunu

and the MongoDB documents used to store and query data from the data warehouse.
# ve veri ambarından (data warehouse) veri depolamak ve sorgulamak için kullanılan MongoDB belgelerini (documents)
```

---

