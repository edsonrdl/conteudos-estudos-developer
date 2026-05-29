# Base de Conhecimento Tecnológico

#tech #knowledge-base #desenvolvimento #infraestrutura

---

## 1. Redes e Infraestrutura

#redes #infraestrutura

### Conceitos Fundamentais

- [ ]  Internet
- [ ]  [[0 TCP IP (Tópicos )|TCP IP]]
- [ ]  DNS
- [ ]  VPN
- [ ]  Firewalls
- [ ]  Proxy
- [ ]  Virtualização

### Arquitetura de Serviços

- [ ]  Load Balance
- [ ]  Service Mesh
- [ ] Service Registry ou Service Discovery
- [ ] Sidecar - Ambassador, Envoy, Nginx
- [ ] mTLS

---

## 2. Protocolos de APIs

#apis #protocolos

- [ ] API
- [ ] SOAP - Simple Object Access Protocol
- [ ] API RESTful - Representational State Transfer
- [ ] WebSockets - Comunicação bidirecional
- [ ] gRPC - Google Remote Procedure Call
- [ ] GraphQL

---

## 3. Desenvolvimento Front-End

#frontend #web

### Linguagens e Tecnologias Base

- [ ]  HTML5
- [ ] CSS
- [ ] Sass
- [ ] JavaScript
- [ ] JSON

### Frameworks e Bibliotecas

- [ ] PWA - Progressive Web App
- [ ] Bootstrap
- [ ] Angular
- [ ] VueJS
- [ ] React
- [ ] jQuery

---

## 4. Design e Experiência do Usuário

#ux #design #ui

### Princípios de Design

- [ ] Usabilidade
- [ ] UI Design
- [ ] UX Design

### Ferramentas de Design

- [ ] Figma
- [ ] Sketch
- [ ] Prototipação
- [ ] Wireframing

---

## 5. Desenvolvimento Back-End

#backend #servidor

### Linguagens e Frameworks

#### Linguagens

- [ ] PHP
- [ ] Java
- [ ] C#
- [ ] C++
- [ ] Node.js (incluindo Express.js)
- [ ] Ruby
- [ ] Python
- [ ] Perl
- [ ] Scala

#### Frameworks

- [ ] .NET Core
- [ ] ASP .NET Core
- [ ] Django
- [ ] Spring Boot

### Arquiteturas de Back-End

- [ ] SOA - Service-Oriented Architecture
- [ ] Microsserviços
- [ ] Arquitetura Hexagonal
- [ ] Clean Architecture
- [ ] Arquitetura MVC

### Padrões de Processamento e Fluxo

- [ ] Middleware e Pipeline — Processamento em cadeia (Curto-circuito, roteamento). Usado em Express, ASP.NET.
- [ ] Command Handler Pattern — Separação extrema de responsabilidades. Uma classe para cada intenção (usado com MediatR/CQRS).
- [ ] Interceptor e Filter — Interceptação via hooks de framework (AOP, Spring, Axios, gRPC).
- [ ] Decorator Pattern aplicado — Como usar Wrappers para adicionar Resiliência (Retry, Cache) sem alterar a regra de negócio.

---

## 6. Mobile Development

#mobile #app

### Linguagens e Frameworks

- [ ] Java (Android)
- [ ] Kotlin (Android)
- [ ] React Native (Cross-platform)
- [ ] Flutter (Cross-platform)

---

## 7. Bancos de Dados e Armazenamento

#database #storage #dados

### Fundamentos e Bancos Relacionais

- [ ] Introdução a banco de dados Relacional
- [ ] SQL

#### Motores:

- [ ] MySQL
- [ ] PostgreSQL
- [ ] SQL Server

### Fundamentos e Bancos Não-Relacionais

- [ ] Introdução a banco de dados Não Relacional
- [ ] NoSQL - Conceito geral

#### Motores:

- [ ] MongoDB
- [ ] Redis

### Escalabilidade e Alta Disponibilidade (Resiliência)

- [ ] Read Replica (Escala de Leitura)
- [ ] Partitioning (Divisão Lógica/Física no mesmo nó)
- [ ] Sharding (Divisão Física distribuída em múltiplos nós)

### Padrões de Arquitetura de Dados e Estado

- [ ] OLTP vs OLAP (Transacional vs Analítico)
- [ ] Event Sourcing (Estado como fluxo de eventos imutáveis)
- [ ] Snapshotting (Otimização de leitura para Event Sourcing)

### Governança e Contratos de Dados

- [ ] Schema Registry (Validação e evolução de schemas)

### Big Data e Arquiteturas Analíticas

#### Arquiteturas:

- [ ] Data Warehouse
- [ ] Data Lake
- [ ] Data Mesh

#### Motores de Processamento:

- [ ] Apache Spark
- [ ] Hadoop

---

## 8. DevOps e Gerenciamento de Infraestrutura

#devops #infraestrutura #ci-cd

### Controle de Versão

- [ ] Git
- [ ] GitHub
- [ ] GitLab
- [ ] Bitbucket
- [ ] GitFlow

### Virtualização e Contêineres

- [ ]  Hipervisores (Hypervisors / VMM)
- [ ] Virtualização
- [ ] Docker
- [ ] Kubernetes
- [ ] Kind
- [ ] Minikube

### Cloud-Native

- [ ] Serverless
- [ ] Contêineres imutáveis e imagens reprodutíveis
- [ ] Autoscaling em app e cluster
- [ ] 12-Factor App
- [ ] Configuration Externalization

#### Infrastructure as Code (IaC)

- [ ]  Terraform
- [ ] Pulumi
- [ ] Ansible

#### Service Mesh

- [ ] Istio
- [ ] Linkerd

### Cloud Computing

- [ ] Google Cloud Platform
- [ ] AWS - Amazon Web Services
- [ ] Azure
- [ ] OpenStack
- [ ] Salesforce - CRM e desenvolvimento em nuvem

### CI/CD - Integração e Deploy Contínuo

- [ ] Jenkins
- [ ] Replicaset
- [ ] Deployment
- [ ] ArgoCD
- [ ] Tekton
- [ ] GitHub Actions
- [ ] GitLab CI/CD

#### Estratégias de Deploy

- [ ] Blue-Green
- [ ] Canary
- [ ] Rolling
- [ ] Progressive Delivery

### Observabilidade

#observabilidade #monitoring

- [ ] Prometheus
- [ ] Grafana
- [ ] ELK Stack (Elasticsearch, Logstash, Kibana)

#### Tracing Distribuído

- [ ] OpenTelemetry
- [ ] Jaeger

#### SRE - Site Reliability Engineering

- [ ] SLI/SLO - Service Level Indicators/Objectives
- [ ] Alertas
- [ ] Error Budget

### Chaos Engineering

#chaos-engineering #resiliencia

**Objetivo:** Validar resiliência injetando falhas controladas

#### Ferramentas

- [ ] LitmusChaos
- [ ] Chaos Mesh
- [ ] Gremlin

#### Casos de Uso

- [ ] Queda de pods
- [ ] Latência de rede
- [ ] Perda parcial de nós

### Gerenciamento de APIs e Serviços

- [ ] Insomnia
- [ ] Firebase

### Interface Gráfica e Gerenciamento de Clusters

- [ ] Lens
- [ ] Rancher
- [ ] K9s

---

## 9. Engenharia de Software

#engenharia-software #processo

### Ciclo de Vida do Desenvolvimento

- [ ] Processos de Desenvolvimento de Software
- [ ] Requisitos de Software
- [ ] Projeto de Software
- [ ] Implementação e Codificação
- [ ] Engenharia de Testes
- [ ] Engenharia de Manutenção de Software

### Qualidade e Configuração

- [ ] Qualidade de Software
- [ ] Gerenciamento de Configuração de Software

### Métricas e SLAs

- [ ] SLI/SLO/SLA
- [ ] Latência
- [ ] Throughput

### Processos de Testes

- [ ] Erros Críticos em Programação

---

## 10. Arquitetura de Software e Design

#arquitetura #design-patterns #performance

### Padrões e Princípios

- [ ] Domain-Driven Design (DDD)
- [ ] DESIGN PATTERNS
- [ ] Princípios SOLID
- [ ] Hexagonal
- [ ] Microservices
- [ ] Clean Architecture
- [ ] MVC
- [ ] Statefull vs Stateless
- [ ] Database per Service
- [ ] Strangler Fig

### Arquitetura Frontend & Mobile

**Arquitetura em Camadas (Frontend)**

- [ ] Feature-Sliced Design (FSD) — Divisão por domínio de negócio
- [ ] `pages/` vs `features/` vs `shared/` — Responsabilidades por camada
- [ ] `app/` → `pages/` → `features/` → `entities/` → `shared/` — Hierarquia de dependências
- [ ] Folder-by-feature vs Folder-by-type — Trade-offs de organização

### Arquitetura Mobile

- [ ] MVVM / MVI / MVP — Padrões de apresentação
- [ ] Modularização — Feature modules vs core modules
- [ ] Offline-first — Sync, conflict resolution, local cache

### BFF — Backend for Frontend

- [ ] O que é BFF — Camada intermediária dedicada a um cliente específico
- [ ] BFF vs API Gateway — Agregação de dados vs roteamento
- [ ] BFF por plataforma — Web BFF, Mobile BFF, TV BFF
- [ ] GraphQL como BFF — Consultas flexíveis orientadas ao cliente
- [ ] Responsabilidades do BFF — Agregação, transformação, autenticação de sessão, cache de borda

### Performance: Latência & Vazão

- [ ] Latência vs Throughput — O trade-off fundamental
- [ ] Latency Budgeting — Tempo máximo aceitável por request.
- [ ] Métricas p95 e p99 — Como medir a experiência real.
- [ ] Táticas de Otimização — (Batching, Paralelismo, Caching).

### Integração e Mensageria

- [ ] Event-Driven Architecture (EDA) - Reações a eventos
- [ ] Event Streaming (Kafka) - Stream contínuo de eventos
- [ ] Kafka vs RabbitMQ - Log distribuído vs message broker
- [ ] Pub Sub
- [ ] EIP & ESB - Enterprise Integration Patterns
- [ ] API Gateway - Roteamento, auth, rate limit
- [ ] API Composition
- [ ] Coreografia vs Orquestração
- [ ] Apache Camel - Enterprise Integration Patterns

### Resiliência e Controle de Fluxo

- [ ] Backpressure - Controle de fluxo upstream
- [ ] Rate Limiting - Limita chamadas por janela
- [ ] API Throttling - Controle de uso (por exemplo, por plano de serviço)
- [ ] Load Shedding - Descarta carga para manter sistema
- [ ] Circuit Breaker - Evita chamar dependências instáveis
- [ ] Retry Pattern - Permite que uma operação falha seja repetida automaticamente
- [ ] Timeout / Deadline Pattern - Define um tempo limite máximo para as chamadas de rede ou operações
- [ ] Fallback Pattern - Fornece uma resposta alternativa (cache, dados padrão).
- [ ] Bulkhead - Isolamento de falhas
- [ ] Caching - Melhoria de latência e disponibilidade

### Dados e Consistência

- [ ] CQRS - Command Query Responsibility Segregation
- [ ] SAGA - Coordenação de transações distribuídas
- [ ] CAP Theorem - Consistency, Availability, Partition tolerance
- [ ] Teorema PACELC
- [ ] Transactional Outbox Pattern - Consistência de dados e eventos em microsserviços
- [ ] BASE vs ACID
- [ ] Consistência Eventual
- [ ] Event Sourcing - Armazena o estado de um aplicativo como uma sequência de eventos imutáveis
- [ ] Leader Election - Escolha de um nó coordenador em um cluster.

### Observabilidade

- **Três Pilares:** Logs, Métricas, Traces
- [ ] Tracing Distribuído - Rastreamento ponta a ponta
- [ ] SLOs & Latency Budget - Metas guiando alertas
- [ ] Correlation ID

---

## 11. Estruturas de Dados e Algoritmos

#algoritmos #estruturas-dados #cs

### Estruturas de Dados

- [ ] Arrays
- [ ] Listas
- [ ] Pilhas
- [ ] Filas
- [ ] Árvores
- [ ] Grafos

### Paradigmas de Programação

- [ ] Orientação a Objetos
- [ ] Programação Funcional

### Mecanismos de Execução

- [ ] Expressões Lambda (Funções Anônimas)
- [ ] Closures
- [ ] Higher-Order Functions
- [ ] Recursão

### Algoritmos e Abordagens Fundamentais

- [ ] Algoritmos de Busca
- [ ] Algoritmos de Ordenação
- [ ] Complexidade de Algoritmos (Big-O)

---

## 12. Plataformas e Sistemas Operacionais

#so #plataformas

### Sistemas Operacionais

- [ ] Windows
- [ ] Linux
- [ ] iOS
- [ ] Android
- [ ] IBM i

### Plataformas de Desenvolvimento

- [ ] Google Platform (desenvolvimento de apps e serviços)

---

## 13. Segurança da Informação

#seguranca #cybersecurity

### Conceitos Fundamentais

- [ ] Criptografia
- [ ] Autenticação e Autorização
- [ ] Chave SSH
- [ ] SSL e TLS — O protocolo prático de segurança.
- [ ] OAuth 2.0
- [ ] OpenID Connect (OIDC)
- [ ] SAML
- [ ] Teste de Penetração
- [ ] Proteção de Dados Pessoais

### Conformidade

- [ ] GDPR - General Data Protection Regulation
- [ ] CCPA - California Consumer Privacy Act

### Controles de Acesso

- [ ] Rate Limiting
- [ ] JWT (Padrão para Tokens de Sessão/Acesso)

---

## 14. Inteligência Artificial e Machine Learning

#ai #ml #machine-learning

### Conceitos e Fundamentos

- [ ] Fundamentos de ML/AI
- [ ] Aprendizado Supervisionado
- [ ] Aprendizado Não Supervisionado
- [ ] Redes Neurais
- [ ] Modelos de Raciocínio (Reasoning Models) 🆕
- [ ] Como LLMs Funcionam — Transformers e Tokenização 🆕
- [ ] Context Window, Tokens e Custo de Inferência 🆕
- [ ] Embeddings — De Texto a Vetores Semânticos 🆕

---

### Ferramentas e Bibliotecas

- [ ] scikit-learn
- [ ] TensorFlow
- [ ] PyTorch
- [ ] Hugging Face Transformers 🆕
- [ ] Ollama — Modelos Locais 🆕

---

### Visão Computacional

- [ ] OpenCV - Processamento de Imagem
- [ ] YOLO - Detecção de Objetos
- [ ] TensorFlow Object Detection API

---

### Multimodalidade 🆕

- [ ] Modelos Multimodais (GPT-4V, Claude, Gemini)
- [ ] RAG Multimodal — Imagens, Áudio e Vídeo
- [ ] Computer Use — Agentes que Controlam Interfaces
- [ ] Voice Agents — Speech-to-Speech

---

### AI Engineering (Engenharia Aplicada)

- [ ] Engenharia de IA Aplicada (AI Engineering) _(contém: LLMs, RAG, Embeddings, Frameworks, Extração, Avaliação, Fine-Tuning, LLMOps)_

---

### Padrões e Arquitetura de IA

- [ ] Retrieval-Augmented Generation (RAG)
- [ ] Agentic Workflows e Orquestração
- [ ] Harness — Runtime de Controle de Agentes
- [ ] Protocolos de Agentes — MCP e A2A
- [ ] Supervisão Humana — HITL, HOTL e HIC
- [ ] Memória de Agentes (Mem0, Zep, MemoryOS)
- [ ] LLMOps e Governança
- [ ] Spec-Driven Development (SDD) 🆕
- [ ] Guardrails e Segurança de IA 🆕
- [ ] Infraestrutura de Inferência — vLLM, Quantização, Routing 🆕

---

### Fine-Tuning e Adaptação de Modelos 🆕

- [ ] Quando Usar Fine-Tuning vs RAG
- [ ] LoRA e QLoRA
- [ ] RLHF e DPO — Alinhamento de Modelos
- [ ] Dados Sintéticos para Fine-Tuning

---

### Governança e Ética em IA 🆕

- [ ] AI TRiSM — AI Trust, Risk and Security Management
- [ ] NIST AI RMF
- [ ] EU AI Act
- [ ] LGPD Aplicada a Sistemas de IA
- [ ] Modelo de Maturidade de Governança de IA

---

### Fundamentos Clássicos (Data Science)

- [ ] Redes Neurais e Deep Learning
- [ ] Visão Computacional
- [ ] Estatística para ML
- [ ] Avaliação de Modelos Clássicos

---

## 15. Análise de Dados e Business Intelligence

#data-analysis #bi #analytics

### Ferramentas de BI e Visualização

- [ ] Power BI
- [ ] Tableau
- [ ] QlikView

### Linguagens e Bibliotecas para Análise

#### Python

- [ ] pandas
- [ ] numpy
- [ ] matplotlib
- [ ] seaborn

#### R

- [ ] R (para estatística avançada)

---

## 16. ETL e Pipelines de Dados

#etl #data-pipeline #data-engineering

### Ferramentas de ETL

- [ ] Apache Kafka
- [ ] Talend
- [ ] Pentaho
- [ ] Apache Spark
- [ ] Hadoop

---

## 17. Automação e RPA

#automacao #rpa #robotic-process-automation

### Ferramentas de Automação

- [ ] Selenium - Automação de testes em navegadores
- [ ] PyAutoGUI - Automação de desktop
- [ ] Automation Anywhere - Processos empresariais
- [ ] BotCity - Automação específica

---

## 18. Testes Automatizados

#testes #qa #testing

### Ferramentas por Categoria

#### Testes Unitários

- [ ] JUnit / TestNG (Java)

#### Testes de Interface

- [ ] Selenium - Testes de UI automatizados

#### Testes de API

- [ ] Insomnia / Newman - Testes de API REST

#### Testes de Performance

- [ ] K6 - Testes de carga para APIs e serviços

---
