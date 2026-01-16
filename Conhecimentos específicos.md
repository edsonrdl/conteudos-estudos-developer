### ⚡ 1. **Low-Latency (Baixa latência)**

> Definição: É o tempo que uma requisição leva para gerar resposta.
> 
> Quanto menor, mais “responsivo” é o sistema.

📌 **Exemplo**:

- Você clica em "comprar ação" e o sistema responde em **30ms**.
- Usado em sistemas financeiros, jogos, IoT, etc.

📉 **Problemas se ignorado**:

- Pode gerar perda de ordens, experiência ruim e até prejuízo.

📊 **Métricas comuns**:

- **Latency p95/p99** (percentis de tempo de resposta).
- **Round Trip Time (RTT)**.

---

### 🚛 2. **High-Throughput (Alta vazão)**

> Definição: É a quantidade de requisições ou eventos que o sistema consegue processar por segundo.

📌 **Exemplo**:

- Kafka processando **100.000 mensagens/segundo**.
- Sistema de monitoramento que coleta dados de milhares de sensores simultaneamente.

📉 **Problemas se ignorado**:

- Filas crescem, eventos se perdem, falhas em cascata.

📊 **Métricas comuns**:

- **Events/sec**, **Requests/sec**, **Bytes/sec**.

---

### ⚖️ 3. **Trade-off: Latência vs Vazão**

> Analogia:
> 
> Imagine um tobogã de água (baixa latência) e um rio com várias boias ao mesmo tempo (alta vazão). Se você quer ambos rápidos e cheios, precisará equilibrar engenharia, recursos e estrutura.

📌 **Dilema comum**:

- Otimizar para latência pode **reduzir a vazão** (processo rápido, mas menos simultâneo).
- Otimizar para vazão pode **aumentar a latência** (mais fila, mais espera).

🎯 **Exemplo técnico**:

- Um sistema Kafka pode aumentar **batch size** para aumentar throughput, mas isso **aumenta a latência** de entrega individual.

---

### 🔀 4. **Event-Driven Architecture (EDA)**

> Definição: Sistemas reagem a eventos (ex: "PedidoCriado"), disparando ações de forma assíncrona.

📌 **Benefícios**:

- Alta escalabilidade.
- Baixo acoplamento.
- Facilita integração de sistemas heterogêneos.

📉 **Desafios**:

- Observabilidade (tracing).
- Ordem dos eventos.
- Garantia de entrega.

---

### 🛰️ 5. **Event Streaming**

> Exemplo prático: Apache Kafka
> 
> Fluxo contínuo de eventos, como um **canal de TV**, onde vários consumidores podem ouvir.

📌 **Uso**:

- Logs, métricas, transações financeiras, alertas.

---

### 🧱 6. **Cloud-Native**

> Aplicações feitas para rodar em nuvem, com escalabilidade horizontal, tolerância a falhas e automação.

📌 Inclui:

- Containers (Docker)
- Orquestradores (Kubernetes)
- Auto-scaling
- Infra como código (Terraform)

---

### 🧠 7. **Design Patterns + GoF**

> Padrões clássicos de projeto para resolver problemas comuns de software.

🔧 Exemplos relevantes para integração:

- **Adapter**: traduz interfaces diferentes.
- **Observer**: eventos assíncronos.
- **Strategy**: seleção dinâmica de algoritmos.
- **Facade**: encapsula complexidade (ideal para APIs internas).

---

### 🔐 8. **Segurança, Observabilidade, Alta Disponibilidade**

|Conceito|Descrição|Ferramentas|
|---|---|---|
|**Segurança**|Criptografia, autenticação, rate limiting|OAuth2, mTLS, JWT|
|**Observabilidade**|Ver o que está acontecendo no sistema|Prometheus, Grafana, ELK|
|**Alta Disponibilidade**|Tolerância a falhas e redundância|Load Balancer, Replica Sets|
|**Recuperação de Desastres**|RPO e RTO claros|Backups, replicações, DR drills|

---

## 🧠 Conceitos adicionais para decisões técnicas melhores

| Conceito                  | O que é                                               | Aplicação                        |
| ------------------------- | ----------------------------------------------------- | -------------------------------- |
| **Backpressure**          | Sistema avisa quando não consegue receber mais        | Kafka, Reactive Streams          |
| **Idempotência**          | Repetir a mesma operação sem efeito colateral         | Requisições de API seguras       |
| **Consistência eventual** | Dados ficam consistentes com o tempo                  | Arquiteturas distribuídas        |
| **CQRS**                  | Separação de leitura e escrita                        | Alta performance, leitura rápida |
| **SAGA Pattern**          | Coordenação de transações distribuídas                | Integração entre microsserviços  |
| **CAP Theorem**           | Escolha entre Consistência, Disponibilidade, Partição | Bancos distribuídos              |
| **Latency Budget**        | Tempo total permitido para cada request               | Design para APIs e integrações   |

---

### ✅ O que estudar para melhorar suas decisões arquiteturais:

|Tópico|Sugestão de Estudo|
|---|---|
|Kafka|Kafka Docs|
|Latência vs Throughput|Martin Kleppmann - Designing Data-Intensive Applications|
|Padrões de Integração|Enterprise Integration Patterns|
|Cloud-Native|Kubernetes, Docker, Helm, Istio|
|Segurança|OWASP, JWT, mTLS, RBAC|
|Arquitetura|DDD, EDA, Microservices, Clean Architecture|
|Observabilidade|OpenTelemetry, Jaeger, Prometheus|

---

# 🧠 Catálogo de Conceitos Arquiteturais e Técnicos Avançados

---

## 🔁 **Arquitetura e Integração**

|Conceito|Descrição|
|---|---|
|**Service Mesh**|Camada de rede para controle de tráfego entre microsserviços (ex: Istio, Linkerd).|
|**API Gateway**|Porta de entrada que gerencia rotas, autenticação, versionamento (ex: Kong, APIGateway).|
|**Service Discovery**|Mecanismo para encontrar serviços dinamicamente (ex: Consul, Eureka).|
|**Enterprise Service Bus (ESB)**|Arquitetura tradicional de integração (ex: MuleESB, WSO2).|
|**EIP (Enterprise Integration Patterns)**|Conjunto de padrões de integração como Splitter, Aggregator, Filter.|
|**Schema Registry**|Registro central de schemas para eventos (ex: Avro, Protobuf no Kafka).|
|**Choreography vs Orchestration**|Integração entre serviços com ou sem um coordenador central.|

---

## ⏱️ **Desempenho e Escalabilidade**

| Conceito                  | Descrição                                                    |
| ------------------------- | ------------------------------------------------------------ |
| **Latency vs Throughput** | Já visto: tempo de resposta vs volume processado.            |
| **Backpressure**          | Controle de fluxo para evitar sobrecarga.                    |
| **Rate Limiting**         | Limite de requisições por tempo (segurança e desempenho).    |
| **Load Shedding**         | Rejeitar requisições para manter o sistema estável.          |
| **Circuit Breaker**       | Bloqueia chamadas a sistemas instáveis.                      |
| **Bulkhead**              | Isola falhas por tipo de operação.                           |
| **Caching**               | Memória temporária para acelerar respostas (ex: Redis, CDN). |

---

## 🔐 **Segurança e Conformidade**

|Conceito|Descrição|
|---|---|
|**Zero Trust Architecture**|Nada é confiável por padrão, verificação constante.|
|**OAuth2 / OIDC**|Autenticação/autorização moderna.|
|**mTLS (Mutual TLS)**|Autenticação mútua entre serviços.|
|**Data Loss Prevention (DLP)**|Estratégias para evitar vazamento de dados.|
|**Auditoria e Trilha de Logs**|Monitoramento completo de acessos e ações (compliance).|
|**Segurança em trânsito e em repouso**|Criptografia TLS e AES para dados.|

---

## 🔎 **Observabilidade e Operações**

|Conceito|Descrição|
|---|---|
|**Logs estruturados**|Logs em formato parseável (JSON) para análise automatizada.|
|**Tracing distribuído**|Rastrear requisições de ponta a ponta (ex: Jaeger, Zipkin).|
|**Alertas baseados em SLOs**|Monitoramento guiado por objetivos de serviço (Service Level Objectives).|
|**Metrics vs Logs vs Traces**|Três pilares da observabilidade.|
|**Chaos Engineering**|Testes proativos de falhas para avaliar resiliência (ex: Gremlin, LitmusChaos).|

---

## 📦 **Armazenamento e Dados**

|Conceito|Descrição|
|---|---|
|**Event Sourcing**|Persistência baseada em eventos (sem sobrescrita de estado).|
|**Snapshotting**|Salvar estados intermediários em sistemas com muitos eventos.|
|**Command Query Responsibility Segregation (CQRS)**|Separação entre comandos (escrevem) e queries (leem).|
|**OLTP vs OLAP**|Processamento transacional (apps) vs analítico (BI).|
|**Read Replica**|Cópias somente leitura para distribuir carga.|
|**Sharding**|Divisão horizontal de dados (ex: por região, cliente).|
|**Partitioning**|Separação lógica dos dados em blocos (ex: Kafka partitions).|

---

## 🧠 **Princípios e Teorias**

| Conceito                    | Descrição                                                                                     |
| --------------------------- | --------------------------------------------------------------------------------------------- |
| **CAP Theorem**             | Não é possível garantir Consistência, Disponibilidade e Tolerância à Partição ao mesmo tempo. |
| **BASE vs ACID**            | BASE (eventualmente consistente) vs ACID (fortemente consistente).                            |
| **Idempotência**            | Mesma operação executada múltiplas vezes não muda o resultado final.                          |
| **Resiliência vs Robustez** | Resiliência: recuperar rápido; Robustez: resistir a falhas.                                   |
| **Fail Fast vs Retry**      | Falhar imediatamente ou tentar de novo.                                                       |
| **Latency Budgeting**       | Tempo máximo aceitável por serviço num request encadeado.                                     |
| **SLI / SLO / SLA**         | Indicadores e metas de qualidade do serviço.                                                  |

---

## 🔄 **Automação e DevOps**

|Conceito|Descrição|
|---|---|
|**Infraestrutura como Código (IaC)**|Definir e versionar infraestrutura (ex: Terraform, Pulumi).|
|**CI/CD Pipelines**|Integração e entrega contínua (GitLab CI, ArgoCD).|
|**Blue-Green Deployment**|Estratégia de deploy sem downtime.|
|**Canary Deployment**|Deploy gradual para pequenos grupos de usuários.|
|**Rollback Automatizado**|Reversão rápida após erro em produção.|
|**Service Level Indicators (SLI)**|Métricas reais de uso (ex: p95 latency).|

---


