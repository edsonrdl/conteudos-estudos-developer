# 📚 Guia de Estudos: AWS Lambda
#tag #aws #cloud #computacao #lambda #serverless

> [!info] Visão Geral
> AWS Lambda é o serviço de computação serverless da AWS: você escreve código, a AWS gerencia toda a infraestrutura. Lambda executa em resposta a eventos, escala de zero a milhares de execuções simultâneas automaticamente e cobra apenas pelo tempo de execução real. Este guia cobre o modelo de execução, cold start, modelos de invocação, triggers nativos, otimização de memória e os casos onde Lambda é — e onde não é — a escolha certa.

---

## 1 Domínio: Fundamentos de Lambda

### 1.1 Modelo Serverless
- **1.1.1. O que é e por que existe**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/1. Fundamentos de Lambda/1.1 Modelo Serverless/1.1.1. O que é e por que existe/1.1.1.1. O que é Lambda, o modelo serverless e como elimina a gestão de infraestrutura de computação|1.1.1.1. O que é Lambda, o modelo serverless e como elimina a gestão de infraestrutura de computação]]

### 1.2 Ciclo de Vida de Execução
- **1.2.1. Init, Invoke e Shutdown**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/1. Fundamentos de Lambda/1.2 Ciclo de Vida de Execução/1.2.1. Init, Invoke e Shutdown/1.2.1.1. Como Lambda inicializa (Init Phase), executa (Invoke) e reutiliza ambientes de execução entre invocações|1.2.1.1. Como Lambda inicializa (Init Phase), executa (Invoke) e reutiliza ambientes de execução entre invocações]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/1. Fundamentos de Lambda/1.2 Ciclo de Vida de Execução/1.2.1. Init, Invoke e Shutdown/1.2.1.2. Cold start — causas por runtime (Python vs Java vs Go), impacto na latência e estratégias de mitigação|1.2.1.2. Cold start — causas por runtime (Python vs Java vs Go), impacto na latência e estratégias de mitigação]]

---

## 2 Domínio: Triggers e Modelos de Invocação

### 2.1 Modelos de Invocação
- **2.1.1. Tipos de invocação**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/2. Triggers e Modelos de Invocação/2.1 Modelos de Invocação/2.1.1. Tipos de invocação/2.1.1.1. Síncrono (API Gateway), assíncrono (S3, SNS) e poll-based (SQS, Kinesis) — diferenças, retry e DLQ|2.1.1.1. Síncrono (API Gateway), assíncrono (S3, SNS) e poll-based (SQS, Kinesis) — diferenças, retry e DLQ]]

### 2.2 Event Sources e Integrações
- **2.2.1. Triggers principais**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/2. Triggers e Modelos de Invocação/2.2 Event Sources e Integrações/2.2.1. Triggers principais/2.2.1.1. API Gateway, S3, SQS, EventBridge, DynamoDB Streams e outros triggers nativos da AWS|2.2.1.1. API Gateway, S3, SQS, EventBridge, DynamoDB Streams e outros triggers nativos da AWS]]

---

## 3 Domínio: Configuração e Performance

### 3.1 Memória e Compute
- **3.1.1. Otimização de recursos**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/3. Configuração e Performance/3.1 Memória e Compute/3.1.1. Otimização de recursos/3.1.1.1. Relação memória-CPU, Lambda Power Tuning, SnapStart (Java), Provisioned Concurrency e limites do serviço|3.1.1.1. Relação memória-CPU, Lambda Power Tuning, SnapStart (Java), Provisioned Concurrency e limites do serviço]]

### 3.2 Deployment e Runtimes
- **3.2.1. Empacotamento e execução**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/3. Configuração e Performance/3.2 Deployment e Runtimes/3.2.1. Empacotamento e execução/3.2.1.1. ZIP vs Container Image, runtimes disponíveis, camadas (Layers) e variáveis de ambiente|3.2.1.1. ZIP vs Container Image, runtimes disponíveis, camadas (Layers) e variáveis de ambiente]]

---

## 4 Domínio: Decisão de Arquitetura

### 4.1 Quando Usar Lambda
- **4.1.1. Casos de uso e anti-padrões**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/4. Decisão de Arquitetura/4.1 Quando Usar Lambda/4.1.1. Casos de uso e anti-padrões/4.1.1.1. Quando Lambda é ideal, quando evitar e comparativo de custo vs EC2 para diferentes perfis de tráfego|4.1.1.1. Quando Lambda é ideal, quando evitar e comparativo de custo vs EC2 para diferentes perfis de tráfego]]

---

> **Links Relacionados:**
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.1. Amazon EC2/0 Amazon EC2 (Tópicos )|Amazon EC2]]

