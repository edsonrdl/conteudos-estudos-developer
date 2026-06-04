# 📚 Guia de Estudos: Amazon ECS
#tag #aws #cloud #computacao #ecs #containers #docker

> [!info] Visão Geral
> Amazon ECS (Elastic Container Service) é o serviço de orquestração de containers da AWS. Permite executar, escalar e gerenciar containers Docker sem precisar instalar ou operar um plano de controle de orquestração. Funciona com dois modelos de execução — EC2 (você gerencia os servidores) e Fargate (serverless, AWS gerencia a infraestrutura) — e integra-se nativamente com ALB, IAM, CloudWatch, ECR e outros serviços AWS. Este guia cobre desde os fundamentos do modelo de execução até a decisão arquitetural entre ECS, EKS e Lambda.

---

## 1 Domínio: Fundamentos e Arquitetura

### 1.1 O que é ECS
- **1.1.1. Conceito e modelo de execução**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/1. Fundamentos e Arquitetura/1.1 O que é ECS/1.1.1. Conceito e modelo de execução/1.1.1.1. O que é ECS, como orquestra containers e diferença entre ECS e Kubernetes (EKS)|1.1.1.1. O que é ECS, como orquestra containers e diferença entre ECS e Kubernetes (EKS)]]

---

## 2 Domínio: Launch Types

### 2.1 EC2 vs Fargate
- **2.1.1. Modelos de execução**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/2. Launch Types/2.1 EC2 vs Fargate/2.1.1. Modelos de execução/2.1.1.1. EC2 Launch Type — controle de infraestrutura, AMIs otimizadas para ECS e quando usar|2.1.1.1. EC2 Launch Type — controle de infraestrutura, AMIs otimizadas para ECS e quando usar]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/2. Launch Types/2.1 EC2 vs Fargate/2.1.1. Modelos de execução/2.1.1.2. Fargate — serverless para containers, modelo de cobrança por vCPU e memória e quando preferir|2.1.1.2. Fargate — serverless para containers, modelo de cobrança por vCPU e memória e quando preferir]]

---

## 3 Domínio: Componentes Principais

### 3.1 Clusters, Tasks e Services
- **3.1.1. Blocos fundamentais**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/3. Componentes Principais/3.1 Clusters, Tasks e Services/3.1.1. Blocos fundamentais/3.1.1.1. Clusters e Task Definitions — estrutura, parâmetros de configuração e versionamento|3.1.1.1. Clusters e Task Definitions — estrutura, parâmetros de configuração e versionamento]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/3. Componentes Principais/3.1 Clusters, Tasks e Services/3.1.1. Blocos fundamentais/3.1.1.2. Tasks e Services — execução única vs serviço de longa duração, Auto Scaling e health checks|3.1.1.2. Tasks e Services — execução única vs serviço de longa duração, Auto Scaling e health checks]]

---

## 4 Domínio: Rede e Integração

### 4.1 Networking e Load Balancing
- **4.1.1. Conectividade**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/4. Rede e Integração/4.1 Networking e Load Balancing/4.1.1. Conectividade/4.1.1.1. awsvpc network mode, integração com ALB e NLB e Service Discovery com Cloud Map|4.1.1.1. awsvpc network mode, integração com ALB/NLB e Service Discovery com Cloud Map]]

---

## 5 Domínio: Segurança e Observabilidade

### 5.1 IAM e Secrets
- **5.1.1. Controle de acesso**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/5. Segurança e Observabilidade/5.1 IAM e Secrets/5.1.1. Controle de acesso/5.1.1.1. Task Role vs Task Execution Role, injeção de secrets do Secrets Manager e SSM em containers|5.1.1.1. Task Role vs Task Execution Role, injeção de secrets do Secrets Manager e SSM em containers]]

### 5.2 Logs e Métricas
- **5.2.1. Observabilidade**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/5. Segurança e Observabilidade/5.2 Logs e Métricas/5.2.1. Observabilidade/5.2.1.1. awslogs driver, Container Insights e métricas de serviço no CloudWatch|5.2.1.1. awslogs driver, Container Insights e métricas de serviço no CloudWatch]]

---

## 6 Domínio: Decisão Arquitetural

### 6.1 ECS vs alternativas
- **6.1.1. Quando usar ECS**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/6. Decisão Arquitetural/6.1 ECS vs alternativas/6.1.1. Quando usar ECS/6.1.1.1. ECS vs EKS vs Lambda vs EC2 — critérios de escolha, trade-offs e perfis de workload|6.1.1.1. ECS vs EKS vs Lambda vs EC2 — critérios de escolha, trade-offs e perfis de workload]]

---

> **Links Relacionados:**
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.1. Amazon EC2/0 Amazon EC2 (Tópicos )|Amazon EC2]]
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/0 AWS Lambda (Tópicos )|AWS Lambda]]
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.4. Amazon EKS/0 Amazon EKS (Tópicos )|Amazon EKS]]
