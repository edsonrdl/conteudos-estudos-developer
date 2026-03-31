# # 📚 Guia de Estudos: Apache Kafka #mensageria #streaming #arquitetura-distribuida #big-data

> [!info] Visão Geral > O Apache Kafka é uma plataforma distribuída de streaming de eventos, focada em altíssimo throughput, escalabilidade horizontal e armazenamento durável. Diferente de message brokers tradicionais, o Kafka atua como um *Commit Log* distribuído e imutável. É o motor definitivo para processamento em tempo real, integração de microsserviços e arquiteturas orientadas a eventos em larga escala.
---

## 1. Introdução ao Apache Kafka

### 1.1. O que é Apache Kafka

- **Definição:** Plataforma distribuída de streaming de eventos
- **Características principais:**
    - Sistema de mensageria publish-subscribe
    - Armazenamento durável de streams de dados
    - Processamento de streams em tempo real
    - Alta taxa de throughput e baixa latência
- **Diferencial:** Commit log distribuído e replicado
- **Escala:** Processa trilhões de eventos por dia em empresas como LinkedIn

### 1.2. História e Evolução

- **2010:** Criado no LinkedIn por Jay Kreps, Neha Narkhede e Jun Rao
- **2011:** Open-sourced e doado para Apache Software Foundation
- **2012:** Graduação como projeto top-level Apache
- **2014:** Fundação da Confluent pelos criadores originais
- **2019:** Introdução do KRaft (Kafka Raft) para substituir ZooKeeper
- **2021-2024:** Adoção massiva em arquiteturas cloud-native e serverless
- **Versões importantes:**
    - 0.8: Replicação
    - 0.10: Kafka Streams
    - 2.8: KRaft preview
    - 3.x: Production-ready KRaft

### 1.3. Casos de Uso do Apache Kafka

- **Event Streaming:**
    - Processamento de eventos em tempo real
    - Event sourcing e CQRS
    - Arquiteturas event-driven
- **Integração de Sistemas:**
    - ETL/ELT pipelines
    - Data integration entre microserviços
    - CDC (Change Data Capture)
- **Processamento de Logs:**
    - Agregação centralizada de logs
    - Análise e monitoramento em tempo real
    - Auditoria e compliance
- **Microserviços:**
    - Comunicação assíncrona entre serviços
    - Saga pattern para transações distribuídas
    - Decoupling de sistemas
- **IoT e Telemetria:**
    - Ingestão de dados de sensores
    - Processamento de métricas em escala
- **Análise em Tempo Real:**
    - Dashboards real-time
    - Detecção de fraudes
    - Recomendações personalizadas

## 2. Arquitetura do Apache Kafka

### 2.1. Componentes Principais

#### 2.1.1. Broker

- **Definição:** Servidor Kafka que armazena e serve dados
- **Responsabilidades:**
    - Receber mensagens de producers
    - Armazenar mensagens em disco
    - Servir mensagens para consumers
    - Participar na replicação
- **Características:**
    - Identificado por broker.id único
    - Pode hospedar múltiplas partições
    - Gerencia leaders e followers
- **Configurações importantes:**

#### 2.1.2. Topic

- **Definição:** Categoria ou feed name para mensagens
- **Estrutura:**
    - Nome único no cluster
    - Dividido em partições
    - Configurações específicas por tópico
- **Naming conventions:**
    - Use lowercase com hífens: `user-events`
    - Evite underscores e caracteres especiais
    - Prefixos por ambiente: `prod.user-events`
- **Configurações essenciais:**
    - retention.ms / retention.bytes
    - segment.bytes / segment.ms
    - cleanup.policy (delete/compact)
    - compression.type

#### 2.1.3. Partition

- **Definição:** Unidade de paralelismo e ordenação
- **Características:**
    - Ordenação garantida dentro da partição
    - Distribuição por key hash ou round-robin
    - Immutable append-only log
- **Estrutura interna:**
    - Segmentos de log
    - Index files
    - Time index
- **Cálculo do número ideal:**

- **Limites práticos:**
    - 4000 partições por broker (máximo)
    - 200K partições por cluster

#### 2.1.4. Producer

- **Definição:** Cliente que publica mensagens nos tópicos
- **Componentes da mensagem:**
    - Key (opcional)
    - Value
    - Headers
    - Timestamp
    - Partition (calculada ou especificada)
- **Processo de envio:**
    1. Serialização
    2. Particionamento
    3. Batching
    4. Compressão
    5. Envio ao broker
- **Garantias de entrega:**
    - At most once (acks=0)
    - At least once (acks=1)
    - Exactly once (idempotent + transactions)

#### 2.1.5. Consumer

- **Definição:** Cliente que lê mensagens dos tópicos
- **Modelo de consumo:**
    - Pull-based (não push)
    - Offset tracking
    - Consumer groups para escalabilidade
- **Processo de leitura:**
    1. Subscribe/assign partições
    2. Poll loop
    3. Processar mensagens
    4. Commit offsets
- **Padrões de consumo:**
    - Sequential processing
    - Parallel processing
    - Batch processing

#### 2.1.6. ZooKeeper (e KRaft)

- **ZooKeeper - Papel tradicional:**
    - Metadata management
    - Controller election
    - Configuration management
    - Cluster membership
    - ACLs storage
- **Limitações do ZooKeeper:**
    - Componente externo adicional
    - Complexidade operacional
    - Limite de escalabilidade
- **KRaft (Kafka Raft) - Novo modelo:**
    - Metadata gerenciado internamente
    - Protocolo Raft para consenso
    - Eliminação de dependência externa
    - Melhor escalabilidade
    - Migração: ZooKeeper → KRaft

### 2.2. Como o Kafka Funciona

#### 2.2.1. Sistema de Pub/Sub

- **Modelo de comunicação:**
    - Producers publicam sem conhecer consumers
    - Consumers subscrevem sem conhecer producers
    - Desacoplamento total
- **Topic como canal:**
    - Múltiplos producers por tópico
    - Múltiplos consumers por tópico
    - Broadcasting e unicasting
- **Durabilidade:**
    - Mensagens persistidas em disco
    - Retention configurável
    - Replay capability

#### 2.2.2. Processamento Assíncrono

- **Benefícios:**
    - Não-bloqueante
    - Melhor utilização de recursos
    - Tolerância a falhas temporárias
- **Implementação:**
    - Producer callbacks
    - Consumer poll model
    - Batch processing
- **Patterns:**
    - Fire-and-forget
    - Request-reply (com correlation ID)
    - Event sourcing

### 2.3. Replicação e Tolerância a Falhas

- **Replication Factor:**
    - Número de cópias de cada partição
    - Padrão recomendado: 3
    - Trade-off: durabilidade vs storage
- **Leader-Follower Model:**
    - Um leader por partição
    - Followers replicam do leader
    - Eleição automática em falhas
- **ISR (In-Sync Replicas):**
    - Replicas sincronizadas
    - min.insync.replicas configuração
    - Garantia de durabilidade
- **Failover:**
    - Detecção automática de falhas
    - Eleição de novo leader
    - Rebalanceamento de partições

## 3. Tópicos no Kafka

### 3.1. O que são Tópicos

- **Conceito fundamental:**
    - Stream de registros categorizados
    - Similar a tabela em banco de dados
    - Multi-subscriber support
- **Características:**
    - Nome único por cluster
    - Configurações independentes
    - Schema opcional (Schema Registry)
- **Criação e gerenciamento:**


### 3.2. Particionamento de Tópicos

- **Estratégias de particionamento:**
    - Round-robin (sem key)
    - Hash-based (com key)
    - Custom partitioner
    - Sticky partitioning (otimização)
- **Considerações de design:**
    - Número de partições vs throughput
    - Ordenação por key
    - Hot partitions avoidance
- **Cálculo de partições:**

- **Reparticionamento:**
    - Adicionar partições (sempre possível)
    - Reduzir partições (não suportado)
    - Impacto na ordenação

### 3.3. Retenção de Mensagens

- **Políticas de retenção:**
    - **Time-based:** retention.ms
    - **Size-based:** retention.bytes
    - **Log compaction:** cleanup.policy=compact
- **Configurações:**

- **Segmentação:**
    - segment.bytes (default 1GB)
    - segment.ms (default 7 dias)
    - Impacto na performance

### 3.4. Replicação de Tópicos

- **Configuração de replicação:**

- **Distribuição de réplicas:**
    - Rack awareness
    - Broker distribution
    - Manual reassignment
- **Monitoramento:**
    - Under-replicated partitions
    - ISR shrink/expand
    - Leader skew

## 4. Produtores no Kafka

### 4.1. O que é um Produtor

- **Definição e responsabilidades:**
    - Cliente que envia dados para Kafka
    - Serialização de dados
    - Escolha de partição
    - Retry logic
- **Ciclo de vida:**
    1. Criar producer instance
    2. Configurar propriedades
    3. Enviar mensagens
    4. Flush/close
- **APIs disponíveis:**
    - Java Client (nativo)
    - librdkafka (C/C++)
    - Clients para Python, Go, .NET, etc.

### 4.2. Envio de Mensagens para Tópicos

- **Métodos de envio:**

- **Batching:**
    - batch.size (bytes)
    - linger.ms (tempo de espera)
    - Trade-off: latência vs throughput
- **Compressão:**
    - none, gzip, snappy, lz4, zstd
    - compression.type configuração

### 4.3. Balanceamento de Carga com Partições

- **Default Partitioner:**
    - Sem key: round-robin/sticky
    - Com key: hash(key) % num_partitions
- **Custom Partitioner:**

- **Sticky Partitioner (2.4+):**
    - Melhor batching
    - Redução de latência
    - Distribuição mais uniforme

### 4.4. Confirmações de Envio (Acknowledgements)

- **Níveis de acks:**
    - **acks=0:** Sem confirmação (fire-and-forget)
    - **acks=1:** Leader confirma
    - **acks=all (-1):** Todos ISR confirmam
- **Configurações relacionadas:**

- **Idempotência:**
    - Evita duplicatas em retries
    - Sequência garantida
    - Overhead mínimo

## 5. Consumidores no Kafka

### 5.1. O que é um Consumidor

- **Definição:**
    - Cliente que lê dados do Kafka
    - Pull-based model
    - Offset tracking
- **Características:**
    - Subscribe ou assign
    - Deserialização de dados
    - Processing guarantees

### 5.2. Grupos de Consumidores

- **Conceito:**
    - Conjunto de consumers cooperando
    - Load balancing automático
    - Fault tolerance
- **Distribuição de partições:**
    - Uma partição por consumer máximo
    - Rebalancing automático
    - Estratégias: Range, RoundRobin, Sticky, Cooperative
- **Configuração:**

### 5.3. Leitura de Mensagens

- **Poll Loop Pattern:**

- **Configurações de fetch:**
    - fetch.min.bytes
    - fetch.max.wait.ms
    - max.partition.fetch.bytes
    - max.poll.records

### 5.4. Controle de Deslocamento (Offset)

- **Tipos de offset:**
    - Current offset (sendo lido)
    - Committed offset (confirmado)
    - Log-end offset (fim do log)
- **Offset reset strategies:**
    - earliest
    - latest
    - none
- **Storage:**
    - __consumer_offsets topic
    - Compacted topic

### 5.5. Commit Manual e Automático

- **Auto commit:**

- **Manual commit:**

- **Trade-offs:**
    - Auto: simples mas risco de reprocessamento
    - Manual: controle fino mas complexidade

## 6. ZooKeeper e KRaft no Kafka

### 6.1. O Papel do ZooKeeper

- **Responsabilidades tradicionais:**
    - Armazenar metadata do cluster
    - Controller election
    - Broker registration
    - Topic configuration
    - ACLs e quotas
    - Consumer offsets (versões antigas)
- **Estrutura no ZooKeeper:**

### 6.2. Gerenciamento de Brokers e Partições

- **Registro de brokers:**
    - Ephemeral nodes
    - Heartbeat mechanism
    - Failure detection
- **Partition assignment:**
    - Leader/follower mapping
    - ISR list management
    - Reassignment operations

### 6.3. Coordenação de Eleições de Líder

- **Controller broker:**
    - Um controller por cluster
    - Gerencia todas eleições
    - Monitora broker failures
- **Eleição de partition leader:**
    - Preferred leader election
    - Unclean leader election
    - Min ISR requirements

### 6.4. Migração para KRaft

- **Benefícios do KRaft:**
    - Eliminação de dependência externa
    - Melhor escalabilidade (milhões de partições)
    - Recuperação mais rápida
    - Operação simplificada
- **Processo de migração:**
    1. Upgrade para versão compatível
    2. Dual-write mode
    3. Migration tools
    4. Cutover
    5. ZooKeeper removal

## 7. Processamento de Streams no Kafka

### 7.1. Introdução ao Kafka Streams

- **O que é:**
    - Biblioteca Java para processamento de streams
    - Parte do Apache Kafka
    - Sem cluster separado necessário
- **Características:**
    - Exactly-once semantics
    - Stateful processing
    - Windowing support
    - Interactive queries
- **Arquitetura:**
    - Stream tasks
    - Stream threads
    - State stores

### 7.2. Diferença entre Kafka Streams e Processamento Tradicional

- **Kafka Streams:**
    - Library, não framework
    - Embedded em aplicação
    - Scaling horizontal simples
    - Fault tolerance automática
- **vs Batch Processing:**
    - Continuous vs periodic
    - Low latency vs high throughput
    - Stream time vs processing time
- **vs Other Stream Processors:**
    - Simplicidade vs features
    - No cluster management
    - Tight Kafka integration

### 7.3. Operações de Streams

#### 7.3.1. Filtragem

#### 7.3.2. Agregação

#### 7.3.3. Junções de Fluxos

### 7.4. Windowing

- **Tipos de janelas:**
    - **Tumbling:** Não sobrepõem, tamanho fixo
    - **Hopping:** Sobrepõem, tamanho e avanço fixos
    - **Sliding:** Atualizam continuamente
    - **Session:** Baseadas em inatividade
- **Implementação:**

### 7.5. State Stores

- **Tipos:**
    - In-memory stores
    - Persistent stores (RocksDB)
    - Window stores
- **Gerenciamento:**
    - Changelog topics
    - State restoration
    - Standby replicas
- **Interactive Queries:**

## 8. Administração do Kafka

### 8.1. Instalação e Configuração do Kafka

#### 8.1.1. Instalação com ZooKeeper

#### 8.1.2. Configuração de Brokers

### 8.2. Monitoramento e Gerenciamento

#### 8.2.1. Ferramentas de Monitoramento

- **JMX Metrics:**
    - Broker metrics
    - Producer/Consumer metrics
    - JVM metrics
- **Ferramentas:**
    - **Prometheus + Grafana:** Métricas e dashboards
    - **Burrow:** Consumer lag monitoring
    - **Kafka Manager/CMAK:** UI para administração
    - **Conduktor:** Plataforma completa
    - **AKHQ:** Web UI moderna
- **Métricas críticas:**
    - Messages in/out rate
    - Byte in/out rate
    - Request rate and latency
    - ISR shrink/expand rate
    - Consumer lag

#### 8.2.2. Monitoramento de Tópicos e Partições

### 8.3. Segurança no Kafka

#### 8.3.1. Autenticação com SSL/TLS

#### 8.3.2. SASL Authentication

- **Mecanismos suportados:**
    - PLAIN
    - SCRAM-SHA-256/512
    - GSSAPI (Kerberos)
    - OAUTHBEARER
- **Configuração SCRAM:**

#### 8.3.3. Controle de Acesso com ACLs

### 8.4. Escalabilidade

#### 8.4.1. Adição de Novos Brokers

1. **Preparar novo broker:**
    - Configurar broker.id único
    - Mesmo zookeeper.connect
    - Configurações compatíveis
2. **Iniciar broker**
3. **Redistribuir partições:**


#### 8.4.2. Rebalanceamento de Partições

- **Criar plano de reassignment:**

- **Executar reassignment:**


- **Monitorar progresso:**


## 9. Integração do Kafka com Outros Sistemas

### 9.1. Integração com Apache Flink

- **Flink Kafka Connector:**

- **Features:**
    - Exactly-once semantics
    - Checkpointing
    - Backpressure handling
    - Event time processing
- **Use cases:**
    - Complex event processing
    - Real-time analytics
    - Machine learning pipelines

### 9.2. Integração com Apache Spark

- **Spark Structured Streaming:**

- **Features:**
    - Micro-batch processing
    - SQL queries on streams
    - ML integration
- **Spark Streaming (DStream):**
    - Direct approach
    - Receiver-based approach

### 9.3. Conectores Kafka Connect

#### 9.3.1. Source Connectors

- **JDBC Source:**

- **Debezium CDC:**
    - MySQL, PostgreSQL, Oracle
    - MongoDB, SQL Server
    - Real-time change capture
- **File Source:**
    - CSV, JSON, Avro
    - Directory watching

#### 9.3.2. Sink Connectors

- **Elasticsearch Sink:**

- **S3 Sink:**
    - Partitioned by time
    - Multiple formats
    - Compression support
- **JDBC Sink:**
    - Upsert/insert modes
    - Schema evolution

### 9.4. REST Proxy e Schema Registry

- **REST Proxy:**
    - HTTP interface para Kafka
    - Produce/consume via REST
    - Útil para linguagens sem client nativo
- **Schema Registry:**
    - Centralização de schemas
    - Versionamento e evolução
    - Compatibilidade enforcement

## 10. Tópicos Avançados

### 10.1. Replicação entre Data Centers

- **MirrorMaker 2.0:**
    - Active-Active replication
    - Active-Passive replication
    - Offset translation
    - Topic renaming
- **Configuração:**

- **Considerações:**
    - Network latency
    - Bandwidth costs
    - Conflict resolution

### 10.2. Compactação de Log

- **Conceito:**
    - Mantém última mensagem por key
    - Útil para snapshots e caches
    - Reduz storage
- **Configuração:**


- **Use cases:**
    - Database changelogs
    - Cache materialization
    - Configuration distribution

### 10.3. Rebalanceamento de Consumidores

- **Triggers:**
    - Consumer join/leave
    - Partition count change
    - Subscription change
- **Estratégias:**
    - **Range:** Partições consecutivas
    - **RoundRobin:** Distribuição uniforme
    - **Sticky:** Minimiza movimento
    - **Cooperative:** Incremental rebalancing
- **Configuração:**


### 10.4. Kafka Streams vs. Apache Flink

| Aspecto          | Kafka Streams              | Apache Flink      |
| ---------------- | -------------------------- | ----------------- |
| Deployment       | Library (embedded)         | Cluster           |
| Complexity       | Simples                    | Complexo          |
| Features         | Básico-intermediário       | Avançado          |
| Latency          | Low ms                     | Sub-ms            |
| State Management | RocksDB                    | Pluggable         |
| SQL Support      | KSQL (separado)            | Table API nativo  |
| Use Cases        | Microserviços, apps médias | Big data, ML, CEP |

### 10.5. Otimização de Desempenho

#### 10.5.1. Configurações de Tópicos

#### 10.5.2. Tuning de Produtores e Consumidores

- **Producer tuning:**

- **Consumer tuning:**


### 10.6. Transações

- **Producer transactions:**


- **Consumer configuration:**


- **Exactly-once semantics:**
    - Idempotent producers
    - Transactional messaging
    - Stream processing guarantees

## 11. Casos de Uso e Boas Práticas

### 11.1. Event Sourcing

- **Conceito:**
    - Armazenar eventos, não estado
    - Rebuild de estado a partir de eventos
    - Auditoria completa
- **Implementação com Kafka:**
    - Topics como event stores
    - Compacted topics para snapshots
    - Kafka Streams para projeções
- **Exemplo prático:**


### 11.2. CQRS (Command Query Responsibility Segregation)

- **Arquitetura:**
    - Commands via Kafka
    - Query models via projections
    - Eventual consistency
- **Implementação:**
    - Command topics
    - Event topics
    - View materialization com Kafka Streams/Connect
- **Benefícios:**
    - Escalabilidade independente
    - Modelos otimizados
    - Flexibilidade

### 11.3. Processamento em Tempo Real

- **Patterns:**
    - Stream enrichment
    - Windowed aggregations
    - Pattern detection
    - Alerting
- **Exemplos:**
    - Detecção de fraude
    - Monitoramento de IoT
    - Real-time dashboards
    - Recomendações

### 11.4. Boas Práticas de Desenvolvimento com Kafka

- **Design de topics:**
    - Um tópico por tipo de evento
    - Naming consistente: `<namespace>.<entity>.<event>`
    - Evitar topics muito granulares
- **Mensagens:**
    - Use schemas (Avro/Protobuf)
    - Inclua metadata em headers
    - Keys significativas para ordenação
- **Error handling:**
    - Dead letter queues
    - Retry topics
    - Circuit breakers
- **Testing:**
    - EmbeddedKafka para testes
    - Testcontainers
    - Consumer lag simulation

