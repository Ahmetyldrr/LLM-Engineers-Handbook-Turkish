#SageMaker'ı Anlamak ve Neden Tercih Ettik?

Bu bölümün son konusu SageMaker'ı anlamak ve neden onu kullanmaya karar verdiğimizi açıklamaktır. SageMaker, makine öğrenimi (ML) modellerini eğitmek ve dağıtmak için kullanılan bir ML platformudur. Resmi tanımı şu şekildedir: 
AWS SageMaker, geliştiricilerin ve veri bilimcilerin makine öğrenimi modellerini büyük ölçekte oluşturmalarını, eğitmelerine ve dağıtmalarına olanak tanıyan AWS tarafından sunulan tamamen yönetilen bir makine öğrenimi hizmetidir (fully managed machine learning service). 
Altta yatan altyapıyı (underlying infrastructure) yöneterek süreci basitleştirir, böylece kullanıcıların yüksek kaliteli modellere verimli bir şekilde odaklanmalarını sağlar. 
SageMaker'ı, GPU kümeleri (clusters of GPUs) üzerinde eğitim hattımızı (training pipeline) ince ayar yapmak (fine-tune) ve operasyon haline getirmek (operationalize) için ve özel LLM Twin modelimizi dünyanın herhangi bir yerinden gerçek zamanlı olarak erişilebilen bir REST API olarak dağıtmak için kullanacağız.

İngilizce Teknik Terimler Parantez İçinde Eklenmiştir:
#SageMaker'ı Anlamak ve Neden Tercih Ettik?

Bu bölümün son konusu SageMaker'ı anlamak ve neden onu kullanmaya karar verdiğimizi açıklamaktır. SageMaker, makine öğrenimi (ML) modellerini eğitmek ve dağıtmak için kullanılan bir ML platformudur. Resmi tanımı şu şekildedir: 
AWS SageMaker, geliştiricilerin ve veri bilimcilerin makine öğrenimi modellerini büyük ölçekte oluşturmalarını, eğitmelerine ve dağıtmalarına olanak tanıyan AWS tarafından sunulan tamamen yönetilen bir makine öğrenimi hizmetidir (fully managed machine learning service). 
Altta yatan altyapıyı (underlying infrastructure) yöneterek süreci basitleştirir, böylece kullanıcıların yüksek kaliteli modellere verimli bir şekilde odaklanmalarını sağlar. 
SageMaker'ı, GPU kümeleri (clusters of GPUs) üzerinde eğitim hattını (training pipeline) ince ayar yapmak (fine-tune) ve operasyon haline getirmek (operationalize) için ve özel LLM Twin modelimizi dünyanın herhangi bir yerinden gerçek zamanlı olarak erişilebilen bir REST API olarak dağıtmak için kullanacağız.

Kodların Ayrıntılı Açıklaması:
```
The last topic of this chapter is understanding SageMaker and why we decided to use it.
```
- Bu satır, bölümün son konusunun SageMaker'ı anlamak ve neden kullanmaya karar verdiklerini açıklamak olduğunu belirtir.

```
SageMaker is an ML platform used to train and deploy ML models.
```
- Bu satır, SageMaker'ın makine öğrenimi modellerini eğitmek ve dağıtmak için kullanılan bir ML platformu olduğunu açıklar.

```
An official definition is as follows: 
```
- Bu satır, SageMaker'ın resmi tanımını sunacağını belirtir.

```
AWS SageMaker is a fully managed machine learning service by AWS that enables developers and data scientists to build, train, and deploy machine learning models at scale.
```
- Bu satır, AWS SageMaker'ın AWS tarafından sunulan, geliştiricilerin ve veri bilimcilerin makine öğrenimi modellerini büyük ölçekte oluşturmalarına, eğitmelerine ve dağıtmalarına olanak tanıyan tamamen yönetilen bir makine öğrenimi hizmeti olduğunu açıklar.
  - `fully managed machine learning service`: Tamamen yönetilen makine öğrenimi hizmeti.
  - `at scale`: Büyük ölçekte.

```
It simplifies the process by handling the underlying infrastructure, allowing users to focus on developing high-quality models efficiently.
```
- Bu satır, SageMaker'ın altta yatan altyapıyı yöneterek süreci nasıl basitleştirdiğini ve kullanıcıların yüksek kaliteli modellere verimli bir şekilde odaklanmalarını sağladığını açıklar.
  - `underlying infrastructure`: Altta yatan altyapı.

```
We will use SageMaker to fine-tune and operationalize our training pipeline on clusters of GPUs and to deploy our custom LLM Twin model as a REST API that can be accessed in real time from anywhere in the world.
```
- Bu satır, SageMaker'ı nasıl kullanacaklarını açıklar.
  - `fine-tune`: İnce ayar yapmak.
  - `operationalize`: Operasyon haline getirmek.
  - `clusters of GPUs`: GPU kümeleri.
  - `training pipeline`: Eğitim hattı.
  - `custom LLM Twin model`: Özel LLM Twin modeli.
  - `REST API`: REST API olarak dağıtmak.
  - `in real time`: Gerçek zamanlı olarak.
  - `from anywhere in the world`: Dünyanın herhangi bir yerinden.

---

