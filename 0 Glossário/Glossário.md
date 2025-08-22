# Base de Conhecimento Tecnológico

#tech #knowledge-base #desenvolvimento #infraestrutura

## 1. Redes e Infraestrutura

#redes #infraestrutura

### Conceitos Fundamentais

- [ ]  [[Internet]] 
- [ ]  [[TCP/IP]]
- [ ]  [[DNS]]
- [ ]  [[VPN]]
- [ ]  [[Firewalls]]
- [ ]  [[Virtualização]]

### Arquitetura de Serviços

- [ ]  [[Load Balance]]
- [ ]  [[Service Mesh]]
- [ ]  [[mTLS]]
- [[Service Discovery]]

---

## 2. Protocolos de APIs

#apis #protocolos

- [[API]] - Conceitos gerais
- [[SOAP]] - Simple Object Access Protocol
- [[API RESTful]] - Representational State Transfer
- [[WebSockets]] - Comunicação bidirecional
- [[gRPC]] - Google Remote Procedure Call

---

## 3. Desenvolvimento Front-End

#frontend #web

### Linguagens e Tecnologias Base

- [[HTML5]]
 - [ ]  [[CSS]] (incluindo [[Sass]] e [[Less]])
- [[JavaScript]]
- [[JSON]]

### Frameworks e Bibliotecas

- [[PWA]] - Progressive Web App
- [[Bootstrap]]
- [[Angular]]
- [[VueJS]]
- [[React]]
- [[jQuery]]

---

## 4. Design e Experiência do Usuário

#ux #design #ui

### Princípios de Design

- [[Usabilidade]]
- [[UI Design]]
- [[UX Design]]

### Ferramentas de Design

- [[Figma]]
- [[Sketch]]
- Prototipação e wireframing

---

## 5. Desenvolvimento Back-End

#backend #servidor

### Linguagens e Frameworks

#### Linguagens

- [[PHP]]
- [[Java]]
- [[C#]]
- [[C++]]
- [[Node.js]] (incluindo [[Express.js]])
- [[Ruby]]
- [[Python]]
- [[Perl]]
- [[Scala]]

#### Frameworks

- [[.NET Core]]
- [[ASP .NET Core]]
- [[Django]]
- [[Spring Boot]]

### Arquiteturas de Back-End

- [[SOA]] - Service-Oriented Architecture
- [[Microsserviços]]
- [[Arquitetura Hexagonal]]
- [[Clean Architecture]]
- [[Arquitetura MVC]]

---

## 6. Mobile Development

#mobile #app

### Linguagens e Frameworks

- [[Java]] (Android)
- [[Kotlin]] (Android)
- [[React Native]] (Cross-platform)
- [[Flutter]] (Cross-platform)

---

## 7. Bancos de Dados e Armazenamento

#database #storage #dados

### Bancos Relacionais

- [[MySQL]]
- [[PostgreSQL]]
- [[SQL Server]]

### Bancos Não-Relacionais

- [[NoSQL]] - Conceito geral
- [[MongoDB]]
- [[Redis]]

### Conceitos Avançados

- [[OLTP]] vs [[OLAP]]
- [[Read Replica]]
- [[Sharding]]
- [[Partitioning]]
- [[Event Sourcing]]
- [[Snapshotting]]
- [[Schema Registry]]

### Big Data

- [[Apache Spark]]
- [[Hadoop]]

---

## 8. DevOps e Gerenciamento de Infraestrutura

#devops #infraestrutura #ci-cd

### Controle de Versão

- [[Git]]
- [[GitHub]]
- [[GitLab]]
- [[Bitbucket]]
- [[GitFlow]]

### Virtualização e Contêineres

- [[Virtualização]] (VM - Máquina Virtual)
- [[Docker]]
- [[Kubernetes]]
- [[Kind]]
- [[Minikube]]

### Cloud-Native

#### Conceitos

- Contêineres imutáveis e imagens reprodutíveis
- Autoscaling em app e cluster
- [[12-Factor App]]
    - Imutabilidade
    - Observabilidade
    - by-design

#### Infrastructure as Code (IaC)

- [[Terraform]]
- [[Pulumi]]
- [[Ansible]]

#### Service Mesh

- [[Istio]]
- [[Linkerd]]

### Cloud Computing

- [[Google Cloud Platform]]
- [[AWS]] - Amazon Web Services
- [[Azure]]
- [[OpenStack]]

### CI/CD - Integração e Deploy Contínuo

- [[Jenkins]]
- [[Replicaset]]
- [[Deployment]]
- [[ArgoCD]]
- [[Tekton]]
- [[GitHub Actions]]
- [[GitLab CI/CD]]

#### Estratégias de Deploy

- [[Blue-Green]]
- [[Canary]]
- [[Rolling]]
- [[Progressive Delivery]]

### Observabilidade

#observabilidade #monitoring

- [[Prometheus]]
- [[Grafana]]
- [[ELK Stack]] (Elasticsearch, Logstash, Kibana)

#### Tracing Distribuído

- [[OpenTelemetry]]
- [[Jaeger]]

#### SRE - Site Reliability Engineering

- [[SLI/SLO]] - Service Level Indicators/Objectives
- Alertas
- Error Budget

### Chaos Engineering

#chaos-engineering #resiliencia

**Objetivo:** Validar resiliência injetando falhas controladas

#### Ferramentas

- [[LitmusChaos]]
- [[Chaos Mesh]]
- [[Gremlin]]

#### Casos de Uso

- Queda de pods
- Latência de rede
- Perda parcial de nós

### Gerenciamento de APIs e Serviços

- [[Postman]]
- [[Firebase]]

### Interface Gráfica e Gerenciamento de Clusters

- [[Lens]]
- [[Rancher]]
- [[K9s]]

---

## 9. Engenharia de Software

#engenharia-software #processo

### Ciclo de Vida do Desenvolvimento

- [[Processos de Desenvolvimento de Software]]
- [[Requisitos de Software]]
- [[Projeto de Software]]
- [[Implementação e Codificação]]
- [[Engenharia de Testes]]
- [[Engenharia de Manutenção de Software]]

### Qualidade e Configuração

- [[Qualidade de Software]]
- [[Gerenciamento de Configuração de Software]]

### Métricas e SLAs

- [[SLI/SLO/SLA]]

### Processos de Testes

- [[Latência]]
- [[Throughput]]

---

## 10. Arquitetura de Software e Design

#arquitetura #design-patterns #performance

### Padrões e Princípios

- [[DESIGN PATTERNS/]] - Padrões de Projeto (GoF)
- [[Princípios SOLID]] - Base para código coeso e flexível
- [[Padrões Arquiteturais]] - Hexagonal, [[10. Arquitetura de Software e Design de Software/Padrões Arquiteturais/Microservices/1. O que é Microservices | Microservices]], Clean, MVC

### Performance: Latência & Vazão

- [[Low-Latency]] vs [[High-Throughput]] - Trade-offs
- [[Latency Budget]] - Tempo máximo por request/serviço
- **Métricas:** p95/p99, RTT, req/s, bytes/s
- **Táticas:** batching, paralelismo, caching

### Integração e Mensageria

- [[Event-Driven Architecture]] (EDA) - Reações a eventos
- [[Event Streaming]] ([[Kafka]]) - Stream contínuo de eventos
- [[Kafka]] vs [[RabbitMQ]] - Log distribuído vs message broker
- [[EIP]] & [[ESB]] - Enterprise Integration Patterns
- [[API Gateway]] - Roteamento, auth, rate limit
- [[Coreografia]] vs [[Orquestração]]

### Resiliência e Controle de Fluxo

- [[Backpressure]] - Controle de fluxo upstream
- [[Rate Limiting]] - Limita chamadas por janela
- [[Load Shedding]] - Descarta carga para manter sistema
- [[Circuit Breaker]] - Evita chamar dependências instáveis
- [[Bulkhead]] - Isolamento de falhas
- [[Caching]] - Melhoria de latência e disponibilidade

### Dados e Consistência

- [[CQRS]] - Command Query Responsibility Segregation
- [[SAGA]] - Coordenação de transações distribuídas
- [[CAP Theorem]] - Consistency, Availability, Partition tolerance
- [[BASE]] vs [[ACID]] - Eventual vs forte consistência
- [[Consistência Eventual]]

### Observabilidade

- **Três Pilares:** [[Logs]], [[Métricas]], [[Traces]]
- [[Tracing Distribuído]] - Rastreamento ponta a ponta
- [[SLOs]] & [[Latency Budget]] - Metas guiando alertas

---

## 11. Estruturas de Dados e Algoritmos

#algoritmos #estruturas-dados #cs

### Estruturas de Dados

- [[Arrays]]
- [[Listas]]
- [[Pilhas]]
- [[Filas]]
- [[Árvores]]
- [[Grafos]]

### Paradigmas de Programação

- [[Orientação a Objetos]]
- [[Programação Funcional]]

### Algoritmos

- [[Algoritmos de Busca]]
- [[Algoritmos de Ordenação]]
- [[Complexidade de Algoritmos]] (Big O)

---

## 12. Plataformas e Sistemas Operacionais

#so #plataformas

### Sistemas Operacionais

- [[Windows]]
- [[Linux]]
- [[iOS]]
- [[Android]]
- [[IBM i]]

### Plataformas de Desenvolvimento

- [[Google Platform]] (desenvolvimento de apps e serviços)

---

## 13. Segurança da Informação

#seguranca #cybersecurity

### Conceitos Fundamentais

- [[Criptografia]]
- [[Autenticação]] e [[Autorização]]
- [[Teste de Penetração]]
- [[Proteção de Dados Pessoais]]

### Conformidade

- [[GDPR]] - General Data Protection Regulation
- [[CCPA]] - California Consumer Privacy Act

### Controles de Acesso

- [[Rate Limiting]]

---

## 14. Inteligência Artificial e Machine Learning

#ai #ml #machine-learning

### Conceitos e Fundamentos

- [[Fundamentos de ML/AI]]
- [[Aprendizado Supervisionado]]
- [[Aprendizado Não Supervisionado]]
- [[Redes Neurais]]

### Ferramentas e Bibliotecas

- [[scikit-learn]]
- [[TensorFlow]]
- [[PyTorch]]

### Visão Computacional

- [[OpenCV]] - Processamento de Imagem
- [[YOLO]] - Detecção de Objetos
- [[TensorFlow Object Detection API]]

---

## 15. Análise de Dados e Business Intelligence

#data-analysis #bi #analytics

### Ferramentas de BI e Visualização

- [[Power BI]]
- [[Tableau]]
- [[QlikView]]

### Linguagens e Bibliotecas para Análise

#### Python

- [[pandas]]
- [[numpy]]
- [[matplotlib]]
- [[seaborn]]

#### R

- [[R]] (para estatística avançada)

---

## 16. ETL e Pipelines de Dados

#etl #data-pipeline #data-engineering

### Ferramentas de ETL

- [[Apache Kafka]]
- [[Talend]]
- [[Pentaho]]
- [[Apache Spark]]
- [[Hadoop]]

---

## 17. Automação e RPA

#automacao #rpa #robotic-process-automation

### Ferramentas de Automação

- [[Selenium]] - Automação de testes em navegadores
- [[PyAutoGUI]] - Automação de desktop
- [[Automation Anywhere]] - Processos empresariais
- [[BotCity]] - Automação específica

---

## 18. Testes Automatizados

#testes #qa #testing

### Ferramentas por Categoria

#### Testes Unitários

- [[JUnit]] / [[TestNG]] (Java)

#### Testes de Interface

- [[Selenium]] - Testes de UI automatizados

#### Testes de API

- [[Postman]] / [[Newman]] - Testes de API REST

#### Testes de Performance

- [[K6]] - Testes de carga para APIs e serviços

---

## Tags para Organização

### Por Área

- `#frontend` `#backend` `#mobile` `#fullstack`
- `#devops` `#sre` `#cloud` `#kubernetes`
- `#database` `#data-engineering` `#big-data`
- `#ai` `#ml` `#analytics` `#bi`
- `#security` `#testing` `#qa`

### Por Tecnologia

- `#javascript` `#python` `#java` `#csharp`
- `#react` `#angular` `#vue` `#node`
- `#docker` `#kubernetes` `#aws` `#gcp`
- `#kafka` `#redis` `#mongodb` `#postgresql`

### Por Conceito

- `#architecture` `#design-patterns` `#microservices`
- `#performance` `#scalability` `#resilience`
- `#observability` `#monitoring` `#logging`

---

_Nota: Cada tópico marcado com [[]] pode ser transformado em uma nota individual no Obsidian para detalhamento específico._