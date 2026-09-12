# Base de Conhecimento Tecnológico

#tech #knowledge-base #desenvolvimento #infraestrutura

---

## 1. Redes e Infraestrutura

#redes #infraestrutura

### Conceitos Fundamentais

- [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/0 Internet (Tópicos )|Internet]]
- [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/0 TCP IP (Tópicos )|TCP IP]]
- [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/0 DNS (Tópicos )|DNS]]
- [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/VPN/0 VPN (Tópicos )|VPN]]
- [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/0 Firewalls (Tópicos )|Firewalls]]
- [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/0 Proxy (Tópicos )|Proxy]]
- [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/0 Virtualização (Tópicos )|Virtualização]]

### Arquitetura de Serviços

- [ ] Load Balance
- [ ] Service Mesh
- [ ] Service Registry ou Service Discovery
- [ ] Sidecar — Ambassador, Envoy, Nginx
- [ ] mTLS

---

## 2. Protocolos de APIs

#apis #protocolos

- [ ] API
- [ ] SOAP — Simple Object Access Protocol
- [ ] API RESTful — Representational State Transfer
- [ ] WebSockets — Comunicação bidirecional
- [ ] gRPC — Google Remote Procedure Call
- [ ] GraphQL

---

## 3. Desenvolvimento Front-End

#frontend #web

### Linguagens e Tecnologias Base

- [ ] HTML5
- [ ] CSS
- [ ] Sass
- [ ] JavaScript
- [ ] JSON

### Frameworks e Bibliotecas

- [ ] PWA — Progressive Web App
- [ ] Bootstrap
- [ ] [[3. Desenvolvimento Front-End/Frameworks e Bibliotecas/ANGULAR/0 Glossário ( Angular )|Angular]]
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

- [ ] SOA — Service-Oriented Architecture
- [ ] Microsserviços
- [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/0 Hexagonal (Glossário )|Arquitetura Hexagonal]]
- [ ] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/0 Clean Architecture (Glossário )|Clean Architecture]]
- [ ] Arquitetura MVC

### Conceitos de Runtime

- [ ] [[5. Desenvolvimento Back-End/Conceitos de Runtime/Garbage Collection/0 Garbage Collection (Tópicos )|Garbage Collection]]

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

#### Motores

- [ ] MySQL
- [ ] PostgreSQL
- [ ] SQL Server

### Fundamentos e Bancos Não-Relacionais

- [ ] Introdução a banco de dados Não Relacional
- [ ] NoSQL — Conceito geral

#### Motores

- [ ] MongoDB
- [ ] Redis

### Escalabilidade e Alta Disponibilidade (Resiliência)

- [ ] Read Replica — Escala de leitura
- [ ] Partitioning — Divisão lógica/física no mesmo nó
- [ ] Sharding — Divisão física distribuída em múltiplos nós

### Padrões de Arquitetura de Dados e Estado

- [ ] OLTP vs OLAP — Transacional vs Analítico
- [ ] Event Sourcing — Estado como fluxo de eventos imutáveis
- [ ] Snapshotting — Otimização de leitura para Event Sourcing

### Governança e Contratos de Dados

- [ ] Schema Registry — Validação e evolução de schemas

### Big Data e Arquiteturas Analíticas

#### Arquiteturas

- [ ] Data Warehouse
- [ ] Data Lake
- [ ] Data Mesh

#### Motores de Processamento

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

- [ ] Hipervisores (Hypervisors / VMM)
- [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Virtualização/0 Virtualização (Tópicos )|Virtualização]]
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

- [ ] Terraform
- [ ] Pulumi
- [ ] Ansible

#### Service Mesh

- [ ] Istio
- [ ] Linkerd

### Cloud Computing

- [ ] Google Cloud Platform
- [ ] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/0 AWS core (Glossário ) | AWS — Amazon Web Services]]
- [ ] Azure
- [ ] OpenStack
- [ ] Salesforce — CRM e desenvolvimento em nuvem

### CI/CD — Integração e Deploy Contínuo

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

- [ ] Prometheus
- [ ] Grafana
- [ ] ELK Stack — Elasticsearch, Logstash, Kibana

#### Tracing Distribuído

- [ ] OpenTelemetry
- [ ] Jaeger

#### SRE — Site Reliability Engineering

- [ ] SLI/SLO — Service Level Indicators/Objectives
- [ ] Alertas
- [ ] Error Budget

### Chaos Engineering

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

- [ ] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/0 Insomnia (Tópicos )|Insomnia]]
- [ ] Postman
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
- [ ] Design Patterns
- [ ] Princípios SOLID
- [x] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Hexagonal/0 Hexagonal (Glossário )|Hexagonal]]
- [ ] Microservices
- [ ] [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Clean architecture/0 Clean Architecture (Glossário )|Clean Architecture]]
- [ ] MVC
- [ ] Stateful vs Stateless
- [ ] Database per Service
- [ ] Strangler Fig
- [ ] Monolítico Modular

### Arquitetura Frontend & Mobile

#### Arquitetura em Camadas (Frontend)

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
- [ ] Latency Budgeting — Tempo máximo aceitável por request
- [ ] Métricas p95 e p99 — Como medir a experiência real
- [ ] Táticas de Otimização — Batching, Paralelismo, Caching

### Integração e Mensageria

- [ ] Event-Driven Architecture (EDA) — Reações a eventos
- [ ] Event Streaming (Kafka) — Stream contínuo de eventos
- [ ] Kafka vs RabbitMQ — Log distribuído vs message broker
- [ ] Pub Sub
- [ ] EIP & ESB — Enterprise Integration Patterns
- [ ] API Gateway — Roteamento, auth, rate limit
- [ ] API Composition
- [ ] Coreografia vs Orquestração
- [ ] Apache Camel — Enterprise Integration Patterns

### Resiliência e Controle de Fluxo

- [ ] Backpressure — Controle de fluxo upstream
- [ ] Rate Limiting — Limita chamadas por janela
- [ ] API Throttling — Controle de uso (por exemplo, por plano de serviço)
- [ ] Load Shedding — Descarta carga para manter sistema
- [ ] Circuit Breaker — Evita chamar dependências instáveis
- [ ] Retry Pattern — Permite que uma operação falha seja repetida automaticamente
- [ ] Timeout / Deadline Pattern — Define um tempo limite máximo para as chamadas de rede ou operações
- [ ] Fallback Pattern — Fornece uma resposta alternativa (cache, dados padrão)
- [ ] Bulkhead — Isolamento de falhas
- [ ] Caching — Melhoria de latência e disponibilidade

### Dados e Consistência

- [ ] CQRS — Command Query Responsibility Segregation
- [ ] SAGA — Coordenação de transações distribuídas
- [ ] [[10. Arquitetura de Software e Design de Software/Dados e Consistência/CAP Theorem/0 CAP Theorem (Glossário )|CAP Theorem — Consistency, Availability, Partition tolerance]]
- [ ] Teorema PACELC
- [ ] Transactional Outbox Pattern — Consistência de dados e eventos em microsserviços
- [ ] BASE vs ACID
- [ ] Consistência Eventual
- [ ] Event Sourcing — Armazena o estado de um aplicativo como uma sequência de eventos imutáveis
- [ ] Leader Election — Escolha de um nó coordenador em um cluster

### Observabilidade

- **Três Pilares:** Logs, Métricas, Traces
- [ ] Tracing Distribuído — Rastreamento ponta a ponta
- [ ] SLOs & Latency Budget — Metas guiando alertas
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

- [ ] Google Platform — Desenvolvimento de apps e serviços

---

## 13. Segurança da Informação

#seguranca #cybersecurity

### Conceitos Fundamentais

- [ ] Criptografia
- [ ] Autenticação e Autorização
- [ ] Chave SSH
- [ ] SSL e TLS — O protocolo prático de segurança
- [ ] OAuth 2.0
- [ ] OpenID Connect (OIDC)
- [ ] SAML
- [ ] Teste de Penetração
- [ ] Proteção de Dados Pessoais

### Conformidade

- [ ] GDPR — General Data Protection Regulation
- [ ] CCPA — California Consumer Privacy Act

### Controles de Acesso

- [ ] Rate Limiting
- [ ] JWT — Padrão para Tokens de Sessão/Acesso

---

## 14. Inteligência Artificial e Machine Learning

#ai #ml #machine-learning

### Conceitos e Fundamentos

- [ ] [[14. Inteligência Artificial e Machine Learning/Fundamentos de ML-AI/0 Fundamentos de ML-AI (Glossário )|Fundamentos de ML/AI]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Aprendizado Supervisionado/0 Aprendizado Supervisionado (Glossário )|Aprendizado Supervisionado]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Aprendizado Não Supervisionado/0 Aprendizado Não Supervisionado (Glossário )|Aprendizado Não Supervisionado]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Redes Neurais/0 Redes Neurais (Glossário )|Redes Neurais]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Modelos de Raciocínio (Reasoning Models)/0 Modelos de Raciocínio (Reasoning Models) (Glossário )|Modelos de Raciocínio (Reasoning Models)]]
- [x] [[14. Inteligência Artificial e Machine Learning/Como LLMs Funcionam/0 Como LLMs Funcionam (Glossário )|Como LLMs Funcionam — Transformers, Tokenização e Embeddings]]

### Ferramentas e Bibliotecas

- [ ] [[14. Inteligência Artificial e Machine Learning/scikit-learn/0 scikit-learn (Glossário )|scikit-learn]]
- [ ] [[14. Inteligência Artificial e Machine Learning/TensorFlow/0 TensorFlow (Glossário )|TensorFlow]]
- [ ] [[14. Inteligência Artificial e Machine Learning/PyTorch/0 PyTorch (Glossário )|PyTorch]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Hugging Face Transformers/0 Hugging Face Transformers (Glossário )|Hugging Face Transformers]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Ollama — Modelos Locais/0 Ollama — Modelos Locais (Glossário )|Ollama — Modelos Locais]]

### Visão Computacional

- [ ] [[14. Inteligência Artificial e Machine Learning/OpenCV — Processamento de Imagem/0 OpenCV — Processamento de Imagem (Glossário )|OpenCV — Processamento de Imagem]]
- [ ] [[14. Inteligência Artificial e Machine Learning/YOLO — Detecção de Objetos/0 YOLO — Detecção de Objetos (Glossário )|YOLO — Detecção de Objetos]]
- [ ] [[14. Inteligência Artificial e Machine Learning/TensorFlow Object Detection API/0 TensorFlow Object Detection API (Glossário )|TensorFlow Object Detection API]]

### Multimodalidade

- [ ] [[14. Inteligência Artificial e Machine Learning/Modelos Multimodais (GPT-4V, Claude, Gemini)/0 Modelos Multimodais (GPT-4V, Claude, Gemini) (Glossário )|Modelos Multimodais (GPT-4V, Claude, Gemini)]]
- [ ] [[14. Inteligência Artificial e Machine Learning/RAG Multimodal — Imagens, Áudio e Vídeo/0 RAG Multimodal — Imagens, Áudio e Vídeo (Glossário )|RAG Multimodal — Imagens, Áudio e Vídeo]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Computer Use — Agentes que Controlam Interfaces/0 Computer Use — Agentes que Controlam Interfaces (Glossário )|Computer Use — Agentes que Controlam Interfaces]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Voice Agents — Speech-to-Speech/0 Voice Agents — Speech-to-Speech (Glossário )|Voice Agents — Speech-to-Speech]]

### AI Engineering (Engenharia Aplicada)

- [ ] [[14. Inteligência Artificial e Machine Learning/AI Engineering (Engenharia Aplicada)/0 Engenharia de IA Aplicada (AI Engineering) ( Glossário )|Engenharia de IA Aplicada (AI Engineering)]] _(contém: LLMs, RAG, Embeddings, Frameworks, Extração, Avaliação, Fine-Tuning, LLMOps)_

### Padrões e Arquitetura de IA

- [ ] [[14. Inteligência Artificial e Machine Learning/Retrieval-Augmented Generation (RAG)/0 Retrieval-Augmented Generation (RAG) (Glossário )|Retrieval-Augmented Generation (RAG)]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Agentic Workflows e Orquestração/0 Agentic Workflows e Orquestração (Glossário )|Agentic Workflows e Orquestração]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Harness — Runtime de Controle de Agentes/0 Harness — Runtime de Controle de Agentes (Glossário )|Harness — Runtime de Controle de Agentes]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Protocolos de Agentes — MCP e A2A/0 Protocolos de Agentes — MCP e A2A (Glossário )|Protocolos de Agentes — MCP e A2A]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Supervisão Humana — HITL, HOTL e HIC/0 Supervisão Humana — HITL, HOTL e HIC (Glossário )|Supervisão Humana — HITL, HOTL e HIC]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Memória de Agentes (Mem0, Zep, MemoryOS)/0 Memória de Agentes (Mem0, Zep, MemoryOS) (Glossário )|Memória de Agentes (Mem0, Zep, MemoryOS)]]
- [ ] [[14. Inteligência Artificial e Machine Learning/LLMOps e Governança/0 LLMOps e Governança (Glossário )|LLMOps e Governança]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Spec-Driven Development (SDD)/0 Spec-Driven Development (SDD) (Glossário )|Spec-Driven Development (SDD)]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Guardrails e Segurança de IA/0 Guardrails e Segurança de IA (Glossário )|Guardrails e Segurança de IA]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Infraestrutura de Inferência — vLLM, Quantização, Routing/0 Infraestrutura de Inferência — vLLM, Quantização, Routing (Glossário )|Infraestrutura de Inferência — vLLM, Quantização, Routing]]

### Fine-Tuning e Adaptação de Modelos

- [ ] [[14. Inteligência Artificial e Machine Learning/Quando Usar Fine-Tuning vs RAG/0 Quando Usar Fine-Tuning vs RAG (Glossário )|Quando Usar Fine-Tuning vs RAG]]
- [ ] [[14. Inteligência Artificial e Machine Learning/LoRA e QLoRA/0 LoRA e QLoRA (Glossário )|LoRA e QLoRA]]
- [ ] [[14. Inteligência Artificial e Machine Learning/RLHF e DPO — Alinhamento de Modelos/0 RLHF e DPO — Alinhamento de Modelos (Glossário )|RLHF e DPO — Alinhamento de Modelos]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Dados Sintéticos para Fine-Tuning/0 Dados Sintéticos para Fine-Tuning (Glossário )|Dados Sintéticos para Fine-Tuning]]

### Governança e Ética em IA

- [ ] [[14. Inteligência Artificial e Machine Learning/AI TRiSM — AI Trust, Risk and Security Management/0 AI TRiSM — AI Trust, Risk and Security Management (Glossário )|AI TRiSM — AI Trust, Risk and Security Management]]
- [ ] [[14. Inteligência Artificial e Machine Learning/NIST AI RMF/0 NIST AI RMF (Glossário )|NIST AI RMF]]
- [ ] [[14. Inteligência Artificial e Machine Learning/EU AI Act/0 EU AI Act (Glossário )|EU AI Act]]
- [ ] [[14. Inteligência Artificial e Machine Learning/LGPD Aplicada a Sistemas de IA/0 LGPD Aplicada a Sistemas de IA (Glossário )|LGPD Aplicada a Sistemas de IA]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Modelo de Maturidade de Governança de IA/0 Modelo de Maturidade de Governança de IA (Glossário )|Modelo de Maturidade de Governança de IA]]

### Fundamentos Clássicos (Data Science)

- [ ] [[14. Inteligência Artificial e Machine Learning/Redes Neurais e Deep Learning/0 Redes Neurais e Deep Learning (Glossário )|Redes Neurais e Deep Learning]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Visão Computacional (Fundamentos)/0 Visão Computacional (Glossário )|Visão Computacional]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Estatística para ML/0 Estatística para ML (Glossário )|Estatística para ML]]
- [ ] [[14. Inteligência Artificial e Machine Learning/Avaliação de Modelos Clássicos/0 Avaliação de Modelos Clássicos (Glossário )|Avaliação de Modelos Clássicos]]

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

- [ ] Selenium — Automação de testes em navegadores
- [ ] PyAutoGUI — Automação de desktop
- [ ] Automation Anywhere — Processos empresariais
- [ ] BotCity — Automação específica

---

## 18. Testes Automatizados

#testes #qa #testing

### Ferramentas por Categoria

#### Testes Unitários

- [ ] JUnit / TestNG (Java)

#### Testes de Interface

- [ ] Selenium — Testes de UI automatizados

#### Testes de API

- [ ] Insomnia / Newman — Testes de API REST

#### Testes de Performance

- [ ] K6 — Testes de carga para APIs e serviços

---
