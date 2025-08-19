# 1. Redes e Infraestrutura

- Internet
    
- TCP/IP
    
- DNS
    
- VPN
    
- Firewalls
    
- Virtualização
    
- **Load Balancer** (balanceamento de carga)
    
- Service Mesh
    
- mTLS (mutual TLS)
    
- Service Discovery
    

# 2. Protocolos de APIs

- API
    
- SOAP
    
- API RESTful
    
- WebSockets
    
- gRPC
    

# 3. Desenvolvimento Front-End

## Linguagens e Tecnologias

- HTML5
    
- CSS (incl. Sass e Less)
    
- JavaScript
    
- JSON
    

## Frameworks e Bibliotecas

- PWA (Progressive Web App)
    
- Bootstrap
    
- Angular
    
- Vue.js
    
- React
    
- jQuery
    

# 4. Design e Experiência do Usuário (UX)

## Princípios de Design

- Usabilidade
    
- UI Design
    
- UX Design
    

## Design de Interfaces e Prototipação

- Ferramentas (ex.: Figma, Sketch)
    

# 5. Desenvolvimento Back-End

## Linguagens e Frameworks

- PHP
    
- Java
    
- .NET Core
    
- ASP.NET Core
    
- Node.js (incl. Express.js)
    
- Ruby
    
- Python
    
- C#
    
- C++
    
- Perl
    
- Scala
    
- Django
    
- Spring Boot
    

## Arquiteturas de Back-End

- SOA (Service-Oriented Architecture)
    
- Microsserviços
    
- Arquitetura Hexagonal
    
- Clean Architecture
    
- Arquitetura MVC
    

# 6. Mobile Development

## Linguagens e Frameworks

- Java
    
- Kotlin
    
- React Native
    
- Flutter
    

# 7. Bancos de Dados e Armazenamento de Dados

## Relacionais e Não-Relacionais

- MySQL
    
- PostgreSQL
    
- SQL Server
    
- NoSQL (conceito geral)
    
- MongoDB
    
- Redis
    
- OLTP
    
- OLAP
    
- Read Replicas
    
- Sharding
    
- Partitioning
    
- Event Sourcing
    
- Snapshotting
    
- Schema Registry
    

## Big Data

- Apache Spark
    
- Hadoop
    

# 8. DevOps e Gerenciamento de Infraestrutura

## Controle de Versão

- Git
    
- GitHub
    
- GitLab
    
- Bitbucket
    
- GitFlow
    

## Virtualização e Contêineres

- Virtualização (VM – Máquina Virtual)
    
- Docker
    
- Kubernetes
    
- Kind
    
- Minikube
    

### Cloud-Native

- Contêineres imutáveis e imagens reprodutíveis
    
- Autoscaling (app e cluster)
    
- IaC (Infra as Code): Terraform, Pulumi, Ansible
    
- 12-Factor App
    
    - Imutabilidade
        
    - Observabilidade
        
    - By design
        
- Service Mesh: Istio, Linkerd
    

## Cloud Computing

- Google Cloud Platform
    
- AWS (Amazon Web Services)
    
- Azure
    
- OpenStack
    

## Integração e Deploy Contínuo

- Jenkins
    
- ReplicaSet
    
- Deployment
    
- Argo CD
    
- Tekton
    
- GitHub Actions
    
- GitLab CI/CD
    
- Estratégias: Blue-Green, Canary, Rolling, Progressive Delivery
    

## Observabilidade

- Prometheus
    
- Grafana
    
- ELK (Elasticsearch, Logstash, Kibana)
    
- Tracing distribuído
    
    - OpenTelemetry
        
    - Jaeger
        
- Sinais de SRE: SLI/SLO, alertas, error budget
    

## Chaos Engineering

- Objetivo: validar resiliência injetando falhas controladas
    
- Ferramentas: LitmusChaos, **Chaos Mesh**, Gremlin
    
- Casos: queda de pods, latência de rede, perda parcial de nós
    

## Gerenciamento de APIs e Serviços

- Postman
    
- Firebase
    

## Interface Gráfica e Gerenciamento de Clusters

- Lens
    
- Rancher
    
- K9s
    

# 9. Engenharia de Software

## Ciclo de Vida do Desenvolvimento de Software

- Processos de Desenvolvimento de Software
    
- Requisitos de Software
    
- Projeto de Software
    
- Implementação e Codificação
    
- Engenharia de Testes
    
- Engenharia de Manutenção de Software
    

## Qualidade e Configuração

- Qualidade de Software
    
- Gerenciamento de Configuração de Software
    

## SLI/SLO/SLA

## Processos de Testes
- Latência
- Throughput (vazão)
# 10. Arquitetura e Design de Software

## Padrões Arquiteturais e de Design
- Design Patterns (GoF)
- Princípios SOLID
- Padrões Arquiteturais: Hexagonal, Microservices, Clean, MVC
## Desempenho: Latência & Vazão
- Low-Latency, High-Throughput, Trade-off — responder rápido vs processar muito
- **Analogia:** tobogã (latência) vs rio cheio de boias (vazão)
- Latency Budget — tempo máximo por request/serviço em cadeia
- Métricas típicas — p95/p99, RTT, req/s, bytes/s
- Táticas — batching, paralelismo, caching (ver “Resiliência”)
## Integração e Mensageria

- Event-Driven Architecture (EDA) — baixo acoplamento
- Event Streaming (Kafka) — stream contínuo de eventos ↔ **16. ETL & Pipelines**
- Kafka 
- RabbitMQ — log distribuído vs message broker
- EIP & ESB — Splitter, Aggregator, Filter; barramento tradicional
- API Gateway — roteamento, auth, rate limit, versionamento
- Coreografia vs Orquestração — sem/com coordenador de fluxos
## Resiliência e Controle de Fluxo

- Backpressure — fonte reduz ritmo quando consumidor satura
    
- Rate Limiting — limita chamadas por janela de tempo
    
- Load Shedding — descarta carga para manter o sistema vivo
    
- Circuit Breaker — evita chamar dependências instáveis
    
- Bulkhead — isola falhas por compartimentos
    
- Caching — evita recomputo/round-trips; melhora latência
    

## Dados e Consistência

- CQRS e SAGA — leitura/escrita separadas; coordenação de transações distribuídas
    
- CAP — trade-offs entre Consistência, Disponibilidade e Partição
    
- BASE vs ACID — consistência eventual vs forte
    
- Consistência eventual — converge com o tempo em sistemas distribuídos
    

## Observabilidade ↔ 8. DevOps e Infra

- Logs / Métricas / Traces — três pilares
    
- Tracing distribuído — request ponta a ponta
    
- SLOs & Latency Budget — metas (p95/p99) guiando alertas
    

# 11. Estruturas de Dados e Algoritmos

## Estruturas de Dados

- Arrays, listas, pilhas, filas, árvores, grafos
    
- Paradigmas de Programação (OO, funcional, etc.)
    

## Algoritmos e Matemática

- Busca e ordenação
    
- Complexidade (Big-O)
    

# 12. Plataformas e Sistemas Operacionais

## Sistemas Operacionais

- Windows
    
- Linux
    
- iOS
    
- Android
    
- IBM i
    

## Plataformas de Desenvolvimento

- Google (para desenvolvimento de apps e serviços)
    

# 13. Segurança da Informação

## Segurança de Sistemas e Redes

- Criptografia
    
- Autenticação e Autorização
    
- Teste de Penetração
    
- Proteção de Dados Pessoais
    
- Conformidade com GDPR/CCPA
    
- Rate Limiting
    

# 14. Inteligência Artificial e Machine Learning

## Conceitos e Fundamentos

- Fundamentos de ML/AI
    
- Aprendizado Supervisionado e Não Supervisionado
    
- Redes Neurais
    

## Ferramentas e Bibliotecas

- scikit-learn
    
- TensorFlow
    
- PyTorch
    

## Visão Computacional

- OpenCV (processamento de imagem)
    
- Detecção de objetos (YOLO, TensorFlow Object Detection API)
    

# 15. Análise de Dados e BI

## Ferramentas de BI e Visualização

- Power BI
    
- Tableau
    
- QlikView
    

## Linguagens e Bibliotecas para Análise

- Python (pandas, numpy, matplotlib, seaborn)
    
- R (estatística avançada)
    

# 16. ETL e Pipelines de Dados

## Ferramentas de ETL

- Apache Kafka
    
- Talend
    
- Pentaho
    
- Apache Spark
    
- Hadoop
    

# 17. Automação e RPA

## Ferramentas

- Selenium (automação de testes em navegador)
    
- PyAutoGUI (automação de desktop)
    
- Automation Anywhere (processos empresariais)
    
- BotCity
    

# 18. Testes Automatizados

## Ferramentas

- JUnit / TestNG (testes unitários para Java)
    
- Selenium (testes de UI)
    
- Postman / Newman (testes de API REST)
    
- K6 (testes de carga para APIs e serviços