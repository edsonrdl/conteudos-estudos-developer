# 📚 Guia de Estudos: Amazon EC2
#tag #aws #cloud #computacao #ec2

> [!info] Visão Geral
> Amazon EC2 (Elastic Compute Cloud) é o serviço de servidores virtuais da AWS — a unidade fundamental de computação da plataforma. Cobre desde a escolha do tipo correto de instância para cada workload (famílias T, M, C, R, I, P e seus derivados Graviton) até os modelos de compra que determinam quanto você paga (On-Demand, Reserved, Savings Plans, Spot e Dedicated). Entender EC2 em profundidade é pré-requisito para qualquer arquitetura AWS.

---

## 1 Domínio: Fundamentos e Arquitetura

### 1.1 O que é EC2
- **1.1.1. Conceito e casos de uso**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.1. Amazon EC2/1. Fundamentos e Arquitetura/1.1 O que é EC2/1.1.1. Conceito e casos de uso/1.1.1.1. O que é EC2, como instâncias virtuais funcionam sobre o Nitro Hypervisor e quando usar EC2 vs Lambda vs containers|1.1.1.1. O que é EC2, como instâncias virtuais funcionam sobre o Nitro Hypervisor e quando usar EC2 vs Lambda vs containers]]

---

## 2 Domínio: Tipos de Instância

### 2.1 Famílias por Perfil de Recurso
- **2.1.1. Propósito Geral (T e M)**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.1. Amazon EC2/2. Tipos de Instância/2.1 Famílias por Perfil de Recurso/2.1.1. Propósito Geral (T e M)/2.1.1.1. Família T — Burstable Performance, CPU Credits e quando usar em desenvolvimento e cargas irregulares|2.1.1.1. Família T — Burstable Performance, CPU Credits e quando usar em desenvolvimento e cargas irregulares]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.1. Amazon EC2/2. Tipos de Instância/2.1 Famílias por Perfil de Recurso/2.1.1. Propósito Geral (T e M)/2.1.1.2. Família M — balanced compute, Graviton vs Intel vs AMD, e casos de uso em produção|2.1.1.2. Família M — balanced compute, Graviton vs Intel vs AMD, e casos de uso em produção]]
- **2.1.2. Compute Optimized (C)**
  - [ ] 2.1.2.1. Família C — alta CPU para transcoding, game servers, ML inference e HPC.
- **2.1.3. Memory Optimized (R, X, z)**
  - [ ] 2.1.3.1. Famílias R, X e z — SAP HANA, bancos in-memory, Spark e quando memória é o gargalo.
- **2.1.4. Storage Optimized (I, D)**
  - [ ] 2.1.4.1. Famílias I e D — NVMe local efêmero, Cassandra, Elasticsearch e data warehousing.
- **2.1.5. Accelerated Computing (P, G, Trn, Inf)**
  - [ ] 2.1.5.1. GPU e chips especializados — ML training, ML inference e renderização com P, G, Trainium e Inferentia.

---

## 3 Domínio: Modelos de Compra

### 3.1 Estratégias de Pricing
- **3.1.1. On-Demand e Reserved Instances**
  - [ ] 3.1.1.1. On-Demand — flexibilidade máxima, cobrança por segundo e quando usar.
  - [ ] 3.1.1.2. Reserved Instances — Standard e Convertible, descontos de até 72%, opções de pagamento e quando comprometer.
- **3.1.2. Savings Plans e Spot**
  - [ ] 3.1.2.1. Compute Savings Plans — compromisso de gasto em $/hora, cobertura automática e vantagem sobre Reserved.
  - [ ] 3.1.2.2. Spot Instances — capacidade ociosa, 70-90% de desconto, estratégias de diversificação e quando usar com segurança.
- **3.1.3. Dedicated**
  - [ ] 3.1.3.1. Dedicated Instances e Dedicated Hosts — compliance, licenças BYOL (Oracle, Windows) e trade-offs de custo.

---

> **Links Relacionados:**
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/0 AWS Lambda (Tópicos )|AWS Lambda]]
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS e EKS/0 Amazon ECS e EKS (Tópicos )|Amazon ECS e EKS]]
