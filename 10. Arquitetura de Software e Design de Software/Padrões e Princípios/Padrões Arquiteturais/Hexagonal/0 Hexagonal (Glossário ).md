# 📚 Guia de Estudos: Arquitetura Hexagonal (Ports & Adapters)
#arquitetura #ports-and-adapters #ddd #clean-architecture

> [!info] Visão Geral
> A Arquitetura Hexagonal, criada por Alistair Cockburn em 2005 sob o nome "Ports and Adapters", propõe isolar completamente a lógica de negócio (o domínio) de qualquer detalhe técnico externo — banco de dados, frameworks web, filas de mensagens, UI. A comunicação entre o núcleo e o mundo externo acontece exclusivamente através de "portas" (interfaces) e "adaptadores" (implementações concretas), permitindo trocar tecnologia sem tocar na regra de negócio.

![[img-hexagonal-2.png]]

---

## 1 Domínio: Fundamentos e Contexto Histórico

### 1.1 Origem e Motivação
- **1.1.1. Alistair Cockburn e o Problema do Acoplamento**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/1. Fundamentos e Contexto Histórico/1.1 Origem e Motivação/1.1.1. Alistair Cockburn e o Problema do Acoplamento/1.1.1.1. Por que a arquitetura hexagonal foi criada e a origem do nome Ports and Adapters|1.1.1.1. Por que a arquitetura hexagonal foi criada e a origem do nome Ports and Adapters]]
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/1. Fundamentos e Contexto Histórico/1.1 Origem e Motivação/1.1.1. Alistair Cockburn e o Problema do Acoplamento/1.1.1.2. Por que o nome hexagonal não tem relação com o número seis|1.1.1.2. Por que o nome hexagonal não tem relação com o número seis]]
- **1.1.2. O Princípio Central: Ports and Adapters**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/1. Fundamentos e Contexto Histórico/1.1 Origem e Motivação/1.1.2. O Princípio Central Ports and Adapters/1.1.2.1. O princípio central Ports and Adapters|1.1.2.1. O princípio central Ports and Adapters]]

### 1.2 Regra de Dependência
- **1.2.1. "O Domínio Não Conhece Ninguém"**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/1. Fundamentos e Contexto Histórico/1.2 Regra de Dependência/1.2.1. O Domínio Não Conhece Ninguém/1.2.1.1. O Domínio Não Conhece Ninguém|1.2.1.1. O Domínio Não Conhece Ninguém]]

---

## 2 Domínio: Anatomia da Arquitetura

### 2.1 Camada de Domínio (Core)
- **2.1.1. Blocos de Construção**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/2. Anatomia da Arquitetura/2.1 Camada de Domínio (Core)/2.1.1. Blocos de Construção/2.1.1.1. Entities, Value Objects, Domain Services e Aggregates|2.1.1.1. Entities, Value Objects, Domain Services e Aggregates]]
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/2. Anatomia da Arquitetura/2.1 Camada de Domínio (Core)/2.1.1. Blocos de Construção/2.1.1.2. Domain Events e Specifications|2.1.1.2. Domain Events e Specifications]]

### 2.2 Camada de Aplicação
- **2.2.1. Orquestração sem Lógica de Negócio**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/2. Anatomia da Arquitetura/2.2 Camada de Aplicação/2.2.1. Orquestração sem Lógica de Negócio/2.2.1.1. Application Services e Use Cases|2.2.1.1. Application Services e Use Cases]]
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/2. Anatomia da Arquitetura/2.2 Camada de Aplicação/2.2.1. Orquestração sem Lógica de Negócio/2.2.1.2. Commands, Queries (CQRS) e DTOs|2.2.1.2. Commands, Queries (CQRS) e DTOs]]

### 2.3 Ports (Portas)
- **2.3.1. Contratos de Entrada e Saída**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/2. Anatomia da Arquitetura/2.3 Ports (Portas)/2.3.1. Contratos de Entrada e Saída/2.3.1.1. Primary Driving Ports|2.3.1.1. Primary/Driving Ports]]
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/2. Anatomia da Arquitetura/2.3 Ports (Portas)/2.3.1. Contratos de Entrada e Saída/2.3.1.2. Secondary Driven Ports|2.3.1.2. Secondary/Driven Ports]]

### 2.4 Adapters (Adaptadores)
- **2.4.1. Adapters de Entrada (Primary/Driving)**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/2. Anatomia da Arquitetura/2.4 Adapters (Adaptadores)/2.4.1. Adapters de Entrada (Primary-Driving)/2.4.1.1. REST, GraphQL, CLI e Message Listener|2.4.1.1. REST, GraphQL, CLI e Message Listener]]
- **2.4.2. Adapters de Saída (Secondary/Driven)**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/2. Anatomia da Arquitetura/2.4 Adapters (Adaptadores)/2.4.2. Adapters de Saída (Secondary-Driven)/2.4.2.1. JPA, MongoDB, SendGrid, Redis e RabbitMQ|2.4.2.1. JPA, MongoDB, SendGrid, Redis e RabbitMQ]]

---

## 3 Domínio: Funcionamento e Fluxo de Requisição

### 3.1 O Caminho de uma Requisição
- **3.1.1. Do Adapter ao Domínio e de Volta**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/3. Funcionamento e Fluxo de Requisição/3.1 O Caminho de uma Requisição/3.1.1. Do Adapter ao Domínio e de Volta/3.1.1.1. O Fluxo Completo de uma Requisição|3.1.1.1. O Fluxo Completo de uma Requisição]]

### 3.2 Guia de Decisão — Onde Colocar Cada Código
- **3.2.1. Mapeamento de Responsabilidades**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/3. Funcionamento e Fluxo de Requisição/3.2 Guia de Decisão - Onde Colocar Cada Código/3.2.1. Mapeamento de Responsabilidades/3.2.1.1. Onde Colocar Cada Tipo de Código|3.2.1.1. Onde Colocar Cada Tipo de Código]]
- **3.2.2. Sinais de Problemas na Arquitetura**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/3. Funcionamento e Fluxo de Requisição/3.2 Guia de Decisão - Onde Colocar Cada Código/3.2.2. Sinais de Problemas na Arquitetura/3.2.2.1. Cinco Anti-padrões e Como Corrigi-los|3.2.2.1. Cinco Anti-padrões e Como Corrigi-los]]

---

## 4 Domínio: Trade-offs e Critérios de Adoção

### 4.1 Vantagens e Custos
- **4.1.1. Ganhos**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/4. Trade-offs e Critérios de Adoção/4.1 Vantagens e Custos/4.1.1. Ganhos/4.1.1.1. Testabilidade, Substituição de Tecnologia e Múltiplas Interfaces|4.1.1.1. Testabilidade, Substituição de Tecnologia e Múltiplas Interfaces]]
- **4.1.2. Custos**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/4. Trade-offs e Critérios de Adoção/4.1 Vantagens e Custos/4.1.2. Custos/4.1.2.1. Overhead de Abstrações e Curva de Aprendizado|4.1.2.1. Overhead de Abstrações e Curva de Aprendizado]]

### 4.2 Quando Usar e Quando Evitar
- **4.2.1. Cenários Favoráveis**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/4. Trade-offs e Critérios de Adoção/4.2 Quando Usar e Quando Evitar/4.2.1. Cenários Favoráveis/4.2.1.1. Quando Usar Arquitetura Hexagonal|4.2.1.1. Quando Usar Arquitetura Hexagonal]]
- **4.2.2. Cenários Desfavoráveis**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/4. Trade-offs e Critérios de Adoção/4.2 Quando Usar e Quando Evitar/4.2.2. Cenários Desfavoráveis/4.2.2.1. Quando Evitar Arquitetura Hexagonal|4.2.2.1. Quando Evitar Arquitetura Hexagonal]]

---

## 5 Domínio: Relação com Outras Arquiteturas

### 5.1 Linha do Tempo e Evolução
- **5.1.1. Hexagonal → Onion → Clean**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/5. Relação com Outras Arquiteturas/5.1 Linha do Tempo e Evolução/5.1.1. Hexagonal, Onion e Clean/5.1.1.1. A Linha do Tempo Evolutiva|5.1.1.1. A Linha do Tempo Evolutiva]]
- **5.1.2. Hexagonal vs Clean Architecture**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/5. Relação com Outras Arquiteturas/5.1 Linha do Tempo e Evolução/5.1.2. Hexagonal vs Clean Architecture/5.1.2.1. Diferenças de Rigidez entre as Duas|5.1.2.1. Diferenças de Rigidez entre as Duas]]

### 5.2 Hexagonal e DDD
- **5.2.1. Camadas que Abrigam o Design Tático**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/5. Relação com Outras Arquiteturas/5.2 Hexagonal e DDD/5.2.1. Camadas que Abrigam o Design Tático/5.2.1.1. Como a Hexagonal Protege o Modelo do DDD|5.2.1.1. Como a Hexagonal Protege o Modelo do DDD]]

### 5.3 Hexagonal e Microsserviços
- **5.3.1. Fronteiras Internas vs Fronteiras de Serviço**
  - [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/5. Relação com Outras Arquiteturas/5.3 Hexagonal e Microsserviços/5.3.1. Fronteiras Internas vs Fronteiras de Serviço/5.3.1.1. Quem Decide os Limites de um Microsserviço|5.3.1.1. Quem Decide os Limites de um Microsserviço]]

---
> **Glossário Pai:** [[0 Glossário/Glossário|Base de Conhecimento Tecnológico]]
>
> **Links Relacionados:**
> [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/🏛️ Clean Architecture vs Hexagonal Architecture - Análise Completa|Clean Architecture vs Hexagonal Architecture]]
> [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Domain Diven Design DDD/0 Domain Diven Design DDD (Glossário )|Domain-Driven Design (DDD)]]
> [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/0 Clean Architecture (Glossário )|Clean Architecture]]
