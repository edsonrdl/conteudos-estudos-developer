# 📚 Guia de Estudos: Amazon S3
#tag #aws #cloud #armazenamento #s3 #storage

> [!info] Visão Geral
> Amazon S3 (Simple Storage Service) é o serviço de armazenamento de objetos da AWS — a fundação de praticamente toda arquitetura de dados na nuvem. Armazena qualquer volume de dados (de bytes a exabytes), com 11 noves de durabilidade (99.999999999%), acesso via HTTP/S, e integração nativa com quase todos os serviços AWS. Cobre desde sites estáticos e backups até data lakes de petabytes e pipelines de ML. Este guia abrange o modelo de objeto, todas as classes de armazenamento, segurança, performance, gerenciamento de ciclo de vida e padrões arquiteturais.

---

## 1 Domínio: Fundamentos e Modelo de Objeto

### 1.1 O que é S3
- **1.1.1. Conceito e arquitetura**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.2. Armazenamento/3.3.2.1. Amazon S3/1. Fundamentos e Modelo de Objeto/1.1 O que é S3/1.1.1. Conceito e arquitetura/1.1.1.1. O que é S3, modelo de objeto (bucket key value), durabilidade, consistência e limites do serviço|1.1.1.1. O que é S3, modelo de objeto (bucket/key/value), durabilidade, consistência e limites do serviço]]

---

## 2 Domínio: Classes de Armazenamento

### 2.1 Hierarquia de Custo e Acesso
- **2.1.1. Standard, Intelligent-Tiering e Infrequent Access**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.2. Armazenamento/3.3.2.1. Amazon S3/2. Classes de Armazenamento/2.1 Hierarquia de Custo e Acesso/2.1.1. Standard, Intelligent-Tiering e Infrequent Access/2.1.1.1. S3 Standard, Intelligent-Tiering, Standard-IA e One Zone-IA — quando usar cada classe de acesso frequente e infrequente|2.1.1.1. S3 Standard, Intelligent-Tiering, Standard-IA e One Zone-IA — quando usar cada classe de acesso frequente e infrequente]]
- **2.1.2. Glacier e Archive**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.2. Armazenamento/3.3.2.1. Amazon S3/2. Classes de Armazenamento/2.1 Hierarquia de Custo e Acesso/2.1.2. Glacier e Archive/2.1.2.1. S3 Glacier Instant, Flexible e Deep Archive — arquivamento de longo prazo, tempos de recuperação e custo|2.1.2.1. S3 Glacier Instant, Flexible e Deep Archive — arquivamento de longo prazo, tempos de recuperação e custo]]

---

## 3 Domínio: Funcionalidades Principais

### 3.1 Segurança e Controle de Acesso
- **3.1.1. Bucket Policies e ACLs**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.2. Armazenamento/3.3.2.1. Amazon S3/3. Funcionalidades Principais/3.1 Segurança e Controle de Acesso/3.1.1. Bucket Policies e ACLs/3.1.1.1. Bucket Policies, ACLs, Block Public Access, pre-signed URLs e criptografia (SSE-S3, SSE-KMS, SSE-C)|3.1.1.1. Bucket Policies, ACLs, Block Public Access, pre-signed URLs e criptografia (SSE-S3, SSE-KMS, SSE-C)]]

### 3.2 Performance e Transferência
- **3.2.1. Multipart Upload e Transfer Acceleration**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.2. Armazenamento/3.3.2.1. Amazon S3/3. Funcionalidades Principais/3.2 Performance e Transferência/3.2.1. Multipart Upload e Transfer Acceleration/3.2.1.1. Multipart Upload, S3 Transfer Acceleration, S3 Select e otimização de performance para objetos grandes|3.2.1.1. Multipart Upload, S3 Transfer Acceleration, S3 Select e otimização de performance para objetos grandes]]

### 3.3 Gerenciamento do Ciclo de Vida
- **3.3.1. Lifecycle Policies e Object Lock**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.2. Armazenamento/3.3.2.1. Amazon S3/3. Funcionalidades Principais/3.3 Gerenciamento do Ciclo de Vida/3.3.1. Lifecycle Policies e Object Lock/3.3.1.1. Lifecycle Policies para transição entre classes, expiração de objetos, Versioning e Object Lock (WORM)|3.3.1.1. Lifecycle Policies para transição entre classes, expiração de objetos, Versioning e Object Lock (WORM)]]

---

## 4 Domínio: Integrações e Casos de Uso

### 4.1 Padrões Arquiteturais
- **4.1.1. Data Lake, Static Website e Event Triggers**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.2. Armazenamento/3.3.2.1. Amazon S3/4. Integrações e Casos de Uso/4.1 Padrões Arquiteturais/4.1.1. Data Lake, Static Website e Event Triggers/4.1.1.1. S3 como data lake, static website hosting, event notifications (Lambda, SQS, SNS) e Replication|4.1.1.1. S3 como data lake, static website hosting, event notifications (Lambda, SQS, SNS) e Replication]]

---

> **Links Relacionados:**
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.2. Armazenamento/3.3.2.2. Amazon EBS/0 Amazon EBS (Tópicos )|Amazon EBS]]
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.2. Armazenamento/3.3.2.3. Amazon EFS/0 Amazon EFS (Tópicos )|Amazon EFS]]
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.2. AWS Lambda/0 AWS Lambda (Tópicos )|AWS Lambda]]
