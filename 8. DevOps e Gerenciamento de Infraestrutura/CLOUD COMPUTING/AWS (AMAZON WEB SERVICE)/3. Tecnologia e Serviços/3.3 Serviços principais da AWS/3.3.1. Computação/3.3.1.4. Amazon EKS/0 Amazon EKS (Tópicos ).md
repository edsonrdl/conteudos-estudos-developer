# 📚 Guia de Estudos: Amazon EKS
#tag #aws #cloud #computacao #eks #kubernetes #containers

> [!info] Visão Geral
> Amazon EKS (Elastic Kubernetes Service) é o serviço gerenciado da AWS para executar Kubernetes. A AWS opera e mantém o plano de controle (control plane) do Kubernetes — o componente mais complexo de operar — enquanto você gerencia os nós de trabalho (EC2 ou Fargate) e os workloads. EKS é a escolha para times que precisam da portabilidade e do ecossistema open-source do Kubernetes com a infraestrutura e integrações nativas da AWS. Este guia cobre desde os fundamentos do modelo de operação até segurança com IRSA, escalamento com Karpenter e as ferramentas essenciais do dia a dia.

---

## 1 Domínio: Fundamentos e Arquitetura

### 1.1 O que é EKS
- **1.1.1. Conceito e modelo de operação**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.4. Amazon EKS/1. Fundamentos e Arquitetura/1.1 O que é EKS/1.1.1. Conceito e modelo de operação/1.1.1.1. O que é EKS, como gerencia o plano de controle Kubernetes e diferença entre EKS e ECS|1.1.1.1. O que é EKS, como gerencia o plano de controle Kubernetes e diferença entre EKS e ECS]]

### 1.2 Componentes do Cluster
- **1.2.1. Plano de controle e plano de dados**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.4. Amazon EKS/1. Fundamentos e Arquitetura/1.2 Componentes do Cluster/1.2.1. Plano de controle e plano de dados/1.2.1.1. Control Plane gerenciado, Node Groups (EC2 e Fargate) e add-ons — como o cluster EKS é composto|1.2.1.1. Control Plane gerenciado, Node Groups (EC2 e Fargate) e add-ons — como o cluster EKS é composto]]

---

## 2 Domínio: Workloads e Escalamento

### 2.1 Objetos Kubernetes no EKS
- **2.1.1. Pods, Deployments e Services**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.4. Amazon EKS/2. Workloads e Escalamento/2.1 Objetos Kubernetes no EKS/2.1.1. Pods, Deployments e Services/2.1.1.1. Pod, Deployment, ReplicaSet e Service — objetos fundamentais para rodar aplicações no EKS|2.1.1.1. Pod, Deployment, ReplicaSet e Service — objetos fundamentais para rodar aplicações no EKS]]
- **2.1.2. Escalamento**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.4. Amazon EKS/2. Workloads e Escalamento/2.1 Objetos Kubernetes no EKS/2.1.2. Escalamento/2.1.2.1. HPA, VPA, Karpenter e Cluster Autoscaler — escalamento horizontal, vertical e de nós|2.1.2.1. HPA, VPA, Karpenter e Cluster Autoscaler — escalamento horizontal, vertical e de nós]]

---

## 3 Domínio: Rede e Acesso

### 3.1 Networking no EKS
- **3.1.1. VPC CNI e Ingress**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.4. Amazon EKS/3. Rede e Acesso/3.1 Networking no EKS/3.1.1. VPC CNI e Ingress/3.1.1.1. AWS VPC CNI, AWS Load Balancer Controller e Ingress — como o tráfego entra e circula no cluster|3.1.1.1. AWS VPC CNI, AWS Load Balancer Controller e Ingress — como o tráfego entra e circula no cluster]]

---

## 4 Domínio: Segurança

### 4.1 IAM e RBAC
- **4.1.1. Controle de acesso**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.4. Amazon EKS/4. Segurança/4.1 IAM e RBAC/4.1.1. Controle de acesso/4.1.1.1. IRSA (IAM Roles for Service Accounts), RBAC e autenticação no EKS — permissões para Pods e usuários|4.1.1.1. IRSA (IAM Roles for Service Accounts), RBAC e autenticação no EKS — permissões para Pods e usuários]]

---

## 5 Domínio: Operação e Ferramentas

### 5.1 CI/CD e Gerenciamento
- **5.1.1. Deploy e manutenção**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.4. Amazon EKS/5. Operação e Ferramentas/5.1 CI/CD e Gerenciamento/5.1.1. Deploy e manutenção/5.1.1.1. kubectl, Helm, eksctl e estratégias de deploy — ferramentas essenciais para operar EKS no dia a dia|5.1.1.1. kubectl, Helm, eksctl e estratégias de deploy — ferramentas essenciais para operar EKS no dia a dia]]

---

> **Links Relacionados:**
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.3. Amazon ECS/0 Amazon ECS (Tópicos )|Amazon ECS]]
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/0 AWS Lambda (Tópicos )|AWS Lambda]]
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.1. Amazon EC2/0 Amazon EC2 (Tópicos )|Amazon EC2]]
