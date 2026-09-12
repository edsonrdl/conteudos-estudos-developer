# 📚 Guia de Estudos: Clean Architecture
#arquitetura #clean-architecture #solid #ddd

> [!info] Visão Geral
> A Clean Architecture, proposta por Robert C. Martin (Uncle Bob) em 2012, sintetiza princípios de arquiteturas anteriores (Hexagonal, Onion) em quatro camadas concêntricas explícitas — Entities, Use Cases, Interface Adapters e Frameworks & Drivers — governadas pela Regra da Dependência: código só pode depender de camadas mais internas, nunca o contrário. O objetivo é produzir sistemas independentes de frameworks, altamente testáveis e resilientes a mudanças de tecnologia, UI ou banco de dados.

---

## 1 Domínio: Fundamentos e Contexto Histórico

### 1.1 Origem e Motivação
- **1.1.1. Robert C. Martin e a Síntese das Arquiteturas**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/1. Fundamentos e Contexto Histórico/1.1 Origem e Motivação/1.1.1. Robert C. Martin e a Síntese das Arquiteturas/1.1.1.1. Robert C. Martin e a Síntese das Arquiteturas|1.1.1.1. Robert C. Martin e a Síntese das Arquiteturas]]

### 1.2 A Regra da Dependência
- **1.2.1. O Princípio Central**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/1. Fundamentos e Contexto Histórico/1.2 A Regra da Dependência/1.2.1. O Princípio Central/1.2.1.1. A Regra da Dependência e o SOLID|1.2.1.1. A Regra da Dependência e o SOLID]]

---

## 2 Domínio: As Quatro Camadas Concêntricas

### 2.1 Entities (Enterprise Business Rules)
- **2.1.1. O Núcleo do Negócio**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/2. As Quatro Camadas Concêntricas/2.1 Entities (Enterprise Business Rules)/2.1.1. O Núcleo do Negócio/2.1.1.1. O que são, responsabilidades e exemplos práticos|2.1.1.1. O que são, responsabilidades e exemplos práticos]]
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/2. As Quatro Camadas Concêntricas/2.1 Entities (Enterprise Business Rules)/2.1.1. O Núcleo do Negócio/2.1.1.2. Quando simplificar e consequências de ignorar|2.1.1.2. Quando simplificar e consequências de ignorar]]

### 2.2 Use Cases (Application Business Rules)
- **2.2.1. Orquestrando as Regras de Aplicação**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/2. As Quatro Camadas Concêntricas/2.2 Use Cases (Application Business Rules)/2.2.1. Orquestrando as Regras de Aplicação/2.2.1.1. O que são, responsabilidades e exemplos práticos|2.2.1.1. O que são, responsabilidades e exemplos práticos]]
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/2. As Quatro Camadas Concêntricas/2.2 Use Cases (Application Business Rules)/2.2.1. Orquestrando as Regras de Aplicação/2.2.1.2. Quando simplificar e consequências de ignorar|2.2.1.2. Quando simplificar e consequências de ignorar]]

### 2.3 Interface Adapters
- **2.3.1. Controllers, Presenters e Gateways**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/2. As Quatro Camadas Concêntricas/2.3 Interface Adapters/2.3.1. Controllers, Presenters e Gateways/2.3.1.1. O que são, responsabilidades e exemplos práticos|2.3.1.1. O que são, responsabilidades e exemplos práticos]]
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/2. As Quatro Camadas Concêntricas/2.3 Interface Adapters/2.3.1. Controllers, Presenters e Gateways/2.3.1.2. Quando simplificar e consequências de vazamento|2.3.1.2. Quando simplificar e consequências de vazamento]]

### 2.4 Frameworks & Drivers
- **2.4.1. Detalhes Isolados na Borda**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/2. As Quatro Camadas Concêntricas/2.4 Frameworks e Drivers/2.4.1. Detalhes Isolados na Borda/2.4.1.1. O que são, responsabilidades e exemplos práticos|2.4.1.1. O que são, responsabilidades e exemplos práticos]]
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/2. As Quatro Camadas Concêntricas/2.4 Frameworks e Drivers/2.4.1. Detalhes Isolados na Borda/2.4.1.2. Quando simplificar e consequências de deixar frameworks ditarem o design|2.4.1.2. Quando simplificar e consequências de deixar frameworks ditarem o design]]

---

## 3 Domínio: Fluxo de Controle e Inversão de Dependência

### 3.1 O Fluxo de uma Requisição
- **3.1.1. Do Controller ao Presenter**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/3. Fluxo de Controle e Inversão de Dependência/3.1 O Fluxo de uma Requisição/3.1.1. Do Controller ao Presenter/3.1.1.1. O Fluxo Completo de uma Requisição|3.1.1.1. O Fluxo Completo de uma Requisição]]

### 3.2 Inversão de Dependência na Prática
- **3.2.1. Implementação Completa em Java/Spring**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/3. Fluxo de Controle e Inversão de Dependência/3.2 Inversão de Dependência na Prática/3.2.1. Implementação Completa em Java-Spring/3.2.1.1. Domínio Puro com Spring Data JPA na Borda|3.2.1.1. Domínio Puro com Spring Data JPA na Borda]]
- **3.2.2. Implementação Completa em C# e Entity Framework**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/3. Fluxo de Controle e Inversão de Dependência/3.2 Inversão de Dependência na Prática/3.2.2. Implementação Completa em C# e Entity Framework/3.2.2.1. IUserRepository, CreateUserUseCase e Teste com Fake Repository|3.2.2.1. IUserRepository, CreateUserUseCase e Teste com Fake Repository]]

---

## 4 Domínio: Padrões Avançados Integrados

### 4.1 CQRS e Event Sourcing
- **4.1.1. Command Query Responsibility Segregation**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/4. Padrões Avançados Integrados/4.1 CQRS e Event Sourcing/4.1.1. Command Query Responsibility Segregation/4.1.1.1. Command Handlers vs Query Handlers|4.1.1.1. Command Handlers vs Query Handlers]]
- **4.1.2. Event Sourcing**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/4. Padrões Avançados Integrados/4.1 CQRS e Event Sourcing/4.1.2. Event Sourcing/4.1.2.1. Reconstrução de Estado a partir de Eventos Imutáveis|4.1.2.1. Reconstrução de Estado a partir de Eventos Imutáveis]]

### 4.2 Domain Events e Saga Pattern
- **4.2.1. Domain Events**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/4. Padrões Avançados Integrados/4.2 Domain Events e Saga Pattern/4.2.1. Domain Events/4.2.1.1. Domain Events e seus Handlers|4.2.1.1. Domain Events e seus Handlers]]
- **4.2.2. Saga Pattern**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/4. Padrões Avançados Integrados/4.2 Domain Events e Saga Pattern/4.2.2. Saga Pattern/4.2.2.1. Saga Pattern - Transações Distribuídas com Compensação|4.2.2.1. Saga Pattern - Transações Distribuídas com Compensação]]

### 4.3 Implementações por Linguagem
- **4.3.1. Estruturas de Pastas Comparadas**
  - [ ] 4.3.1.1. Comparativo de estrutura de pastas e Use Case equivalente em Java/Spring Boot, Python/FastAPI e TypeScript/NestJS.

### 4.4 Testes e Validação Arquitetural
- **4.4.1. Testes por Camada**
  - [ ] 4.4.1.1. Testes de domínio puros (sem dependências) vs testes de aplicação com mocks/doubles.
- **4.4.2. Testes de Arquitetura com ArchUnit**
  - [ ] 4.4.2.1. Como validar automaticamente a Regra da Dependência entre camadas, impedindo violações em tempo de build.

### 4.5 Anti-padrões Comuns
- **4.5.1. Over-engineering e Anemic Domain Model**
  - [ ] 4.5.1.1. Forçar todas as camadas em um CRUD simples, e Entidades sem comportamento (apenas getters/setters).

---

## 5 Domínio: Clean Architecture em Escala

### 5.1 Microsserviços com Clean Architecture
- **5.1.1. Cada Serviço com sua Própria Clean Architecture**
  - [ ] 5.1.1.1. Estrutura por microsserviço (Domain/Application/Infrastructure/API) e comunicação via Integration Events entre serviços.

### 5.2 Monolito Modular Multi-módulo
- **5.2.1. Exemplo Real: Módulos Core, Application, Infrastructure e WebAPI**
  - [ ] 5.2.1.1. Estudo de caso de um projeto Maven multi-módulo real, com motor de workflow (Temporal) isolado na Infraestrutura.

### 5.3 Estratégias de Migração
- **5.3.1. Strangler Fig Pattern**
  - [ ] 5.3.1.1. Migração gradual de um sistema legado para Clean Architecture usando proxy, feature toggle e Anti-corruption Layer.

---

## 6 Domínio: Relação com Outras Arquiteturas

### 6.1 Clean vs Hexagonal vs Onion
- **6.1.1. Comparativo Direto**
  - [ ] 6.1.1.1. Tabela comparativa de foco, estrutura e complexidade entre as três, e critérios para escolher cada uma.

---

> **Glossário Pai:** [[0 Glossário/Glossário|Base de Conhecimento Tecnológico]]
>
> **Links Relacionados:**
> [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/0 Hexagonal (Glossário )|Arquitetura Hexagonal]]
> [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Domain Diven Design DDD/0 Domain Diven Design DDD (Glossário )|Domain-Driven Design (DDD)]]
> [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/SOLID|Princípios SOLID]]
> [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Monolítico Modular vs Microsserviços|Monolítico Modular vs Microsserviços]]
