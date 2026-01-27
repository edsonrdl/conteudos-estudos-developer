## Resumo Executivo

A implementação de sistemas de streaming de eventos distribuídos no ecossistema de nuvem contemporâneo transcendeu a simples escolha de software para se tornar uma disciplina complexa de arquitetura de sistemas. O Apache Kafka, onipresente como a espinha dorsal de pipelines de dados em tempo real, apresenta desafios únicos quando transplantado para a infraestrutura elástica da Amazon Web Services (AWS). Diferente de aplicações _stateless_ que se adaptam nativamente à volatilidade da nuvem, o Kafka é um sistema de armazenamento persistente e ordenado, exigindo garantias estritas de durabilidade de dados e consistência que frequentemente colidem com as abstrações de rede e computação da AWS.

Este relatório técnico oferece uma dissecação profunda das três modalidades primárias de implantação do Kafka na AWS: o serviço gerenciado Amazon Managed Streaming for Apache Kafka (MSK), a orquestração autogerenciada em Amazon Elastic Compute Cloud (EC2) e a implementação conteinerizada via Amazon Elastic Kubernetes Service (EKS). A análise não se limita a uma comparação superficial de funcionalidades, mas penetra na física da infraestrutura — desde a latência de replicação de logs em nível de bloco no EBS, passando pela topologia de rede cross-AZ (Zona de Disponibilidade), até as implicações financeiras de segunda ordem causadas por padrões de tráfego de replicação ineficientes.

O documento é estruturado para guiar arquitetos e engenheiros desde cenários de baixa complexidade — clusters de desenvolvimento efêmeros — até arquiteturas de missão crítica distribuídas geograficamente, com Fator de Replicação (RF) elevado e estratégias de _Rack Awareness_ que abrangem tanto brokers quanto consumidores para mitigar a latência e os custos de transferência de dados, frequentemente descritos como o "assassino silencioso" de orçamentos de dados na nuvem.

---

## 1. Fundamentos Arquiteturais do Kafka na Infraestrutura AWS

Para compreender as nuances operacionais do Kafka na AWS, é imperativo primeiro estabelecer como os primitivos da infraestrutura de nuvem interagem com o protocolo do Kafka. O Apache Kafka não é apenas uma fila de mensagens; é um log de commit distribuído. Sua performance e confiabilidade dependem intrinsecamente de três recursos físicos: I/O de disco (para persistência sequencial), memória RAM (para o Page Cache do sistema operacional) e largura de banda de rede (para replicação síncrona).

### 1.1 A Física das Zonas de Disponibilidade (AZs) e o Modelo de Falha

Na AWS, uma Região é composta por múltiplas Zonas de Disponibilidade (AZs). Cada AZ é isolada fisicamente e energeticamente, funcionando como um data center independente. Para o Kafka, que foi desenhado para ser "consciente de rack" (_rack-aware_), a AZ é a unidade atômica de isolamento de falhas.

A replicação de dados no Kafka ocorre via protocolo TCP. Quando um produtor envia uma mensagem para o líder de uma partição, e a configuração `acks=all` está ativa, o líder não confirma a gravação até que todas as réplicas no conjunto de In-Sync Replicas (ISR) tenham replicado a mensagem. Em uma arquitetura Multi-AZ, isso significa que a latência de gravação ("produce latency") é limitada pela velocidade da luz e pelo processamento de rede entre os data centers da AWS. Embora a latência entre AZs seja tipicamente baixa (< 2ms), ela é ordens de magnitude superior à comunicação intra-AZ.

O desafio arquitetural central reside no fato de que a AWS cobra pelo tráfego de dados que cruza as fronteiras das AZs. Em um cluster Kafka mal configurado, onde líderes e seguidores (followers) estão distribuídos aleatoriamente sem consciência topológica, o tráfego de replicação pode gerar custos exorbitantes, muitas vezes superando o custo da própria computação.

### 1.2 O Dilema do Armazenamento: Rede vs. Local

A escolha do meio de persistência define o perfil de recuperação de desastres (DR) do cluster.

- **Elastic Block Store (EBS):** É o armazenamento padrão da nuvem, desacoplando os dados da instância de computação. Volumes como `gp3` e `io2 Block Express` oferecem durabilidade e a capacidade de "reviver" um broker falho sem precisar reconstruir seus dados do zero, apenas remontando o volume em uma nova instância. No entanto, o EBS compete por largura de banda de rede com o tráfego de replicação do Kafka, criando um gargalo potencial em instâncias menores.
    
- **Instance Store (NVMe):** Discos fisicamente acoplados ao host. Oferecem latência de microssegundos e milhões de IOPS, eliminando o gargalo de rede do armazenamento. A contrapartida é a efemeridade: se a instância EC2 é interrompida ("stop/start") ou falha, os dados são perdidos irrecuperavelmente. Isso exige que o Kafka reduntante regenere os dados via rede a partir de outras réplicas, colocando pressão imensa no _backplane_ de rede durante eventos de recuperação.
    

---

## 2. Amazon MSK: A Abordagem Gerenciada e Suas Camadas de Abstração

O Amazon MSK (Managed Streaming for Apache Kafka) representa a tentativa da AWS de transformar o Kafka em uma "commodity", removendo a carga cognitiva da gestão de infraestrutura. No entanto, "gerenciado" não significa "caixa preta"; entender os internos do MSK é vital para operações de escala.

### 2.1 Arquitetura do Plano de Controle e Dados

O MSK opera em uma arquitetura de responsabilidade compartilhada modificada. A AWS gerencia o plano de controle — o quórum do Apache ZooKeeper (ou controladores KRaft em versões mais recentes) e a orquestração dos brokers. Esses componentes residem em uma VPC gerenciada pela AWS, invisível ao cliente. O plano de dados — os brokers onde os dados residem — é projetado na VPC do cliente através de Elastic Network Interfaces (ENIs). Isso permite que o tráfego de dados permaneça privado, respeitando as regras de Security Group e Network ACLs do cliente, enquanto a AWS mantém a capacidade de substituir hardware falho e aplicar patches de segurança.

### 2.2 Tipos de Cluster e Perfis de Performance

#### 2.2.1 MSK Provisioned: Standard vs. Express Brokers

No modo provisionado, o arquiteto deve dimensionar explicitamente o cluster. A introdução dos **Express Brokers** alterou fundamentalmente a equação de performance do MSK.

- **Standard Brokers:** Utilizam instâncias EC2 subjacentes (como `m5.large` ou `r5.xlarge`) e volumes EBS padrão. A performance é previsível mas limitada pelas características da instância EC2 correspondente. A recuperação de um broker falho pode levar minutos, pois envolve provisionar uma nova instância, anexar o volume EBS e reiniciar o processo Java.
    
- **Express Brokers:** Baseados na tecnologia do AWS Elastic Block Store otimizado (provavelmente utilizando variantes do protocolo SRD - Scalable Reliable Datagram). Eles oferecem até 3x mais throughput por nó comparado ao Standard. O diferencial crítico é o **Rebalanceamento Inteligente**. Em clusters tradicionais, a adição de um broker exige a movimentação manual de partições (via `kafka-reassign-partitions.sh`) para distribuir a carga. Express Brokers rastreiam a carga de partições e movem lideranças e dados automaticamente para mitigar _hotspots_, proporcionando uma elasticidade muito mais fluida e recuperação 90% mais rápida.
    

#### 2.2.2 MSK Serverless: Abstração Radical

O MSK Serverless elimina o conceito de "broker" da perspectiva do usuário. A capacidade de throughput e armazenamento escala automaticamente em resposta à demanda.

- **Mecanismo:** A AWS monitora métricas de _bytes-in_ e utilização de CPU, provisionando partições lógicas em um pool de recursos físicos compartilhados (multi-tenant com isolamento lógico).
    
- **Limitações:** O acesso a configurações profundas do Kafka (`server.properties`) é bloqueado. Mecanismos de autenticação legados não são suportados, exigindo o uso de IAM. É ideal para cargas de trabalho imprevisíveis ou esporádicas, mas pode ser mais caro e menos flexível que clusters provisionados bem otimizados para cargas constantes e previsíveis.
    

### 2.3 Tiered Storage: A Revolução na Retenção de Dados

Historicamente, o custo de armazenamento no Kafka era linear: reter dados por mais tempo exigia discos maiores. Como o Kafka acopla processamento e armazenamento no mesmo nó, discos maiores frequentemente exigiam instâncias maiores ou mais brokers, inflacionando o custo de computação desnecessariamente.

O **Tiered Storage** do MSK desacopla essa relação. O cluster possui dois níveis de armazenamento:

1. **Primary Tier (Performance):** EBS de baixa latência, onde residem os segmentos de log ativos e recentes.
    
2. **Recovery Tier (Custo):** Amazon S3. Segmentos de log fechados e antigos são movidos assincronamente para o S3.
    

**Impacto Econômico e Operacional:** Isso permite retenção virtualmente infinita a custos de S3 ($0.023/GB/mês vs $0.08/GB/mês do EBS gp3). Consumidores que leem dados em tempo real acessam o EBS com latência de milissegundos. Consumidores de "backfill" (que reprocessam histórico) leem transparentemente do S3. A latência de leitura do S3 é maior (centenas de ms para o primeiro byte), mas o throughput é massivo, adequado para jobs batch.

### 2.4 Conectividade Avançada: Cross-Account e Cross-Region

A segurança do MSK impõe que ele seja acessível apenas dentro da VPC. Para cenários corporativos complexos, isso é uma barreira.

- **PrivateLink:** A solução recomendada para acesso Cross-Account. Exige a criação de um Network Load Balancer (NLB) na frente de _cada_ broker do cluster. Isso ocorre porque o protocolo Kafka exige que o cliente se conecte a um broker específico (o líder da partição). O cliente faz uma requisição de metadados, recebe o endereço do líder e precisa conseguir rotear para esse endereço específico. O PrivateLink com NLBs mapeia portas ou IPs para brokers individuais, permitindo essa comunicação sem expor a VPC.
    
- **Transit Gateway:** Simplifica a topologia "Hub-and-Spoke", permitindo que múltiplas VPCs acessem o MSK central. Introduz latência adicional de "hop" e custos de processamento de dados por GB, mas evita a complexidade de gerenciar centenas de peering connections.
    

---

## 3. Kafka Autogerenciado no EC2: Controle Máximo e Responsabilidade Total

Para organizações com requisitos extremos de performance ou customização que o MSK não atende (ex: versões _nightly_ do Kafka, patches customizados, uso de discos locais massivos), rodar Kafka diretamente no EC2 é a alternativa.

### 3.1 Seleção de Instâncias: A Busca pelo Hardware Perfeito

O Kafka é intensivo em recursos, mas de forma assimétrica.

- **Família R (Memory Optimized):** As instâncias `r6g` ou `r7g` (Graviton) são frequentemente as favoritas. O Kafka utiliza agressivamente o Page Cache do Linux para armazenar dados "quentes" na RAM, evitando leituras de disco. Mais RAM significa mais dados servidos diretamente da memória, reduzindo latência. Os processadores Graviton (ARM64) oferecem uma relação preço-performance superior, com custos cerca de 20% menores que equivalentes x86.
    
- **Família I (Storage Optimized):** Instâncias como `i3en` ou `im4gn` vêm equipadas com dezenas de Terabytes de SSD NVMe local. Para clusters que ingerem Petabytes, o custo por GB do EBS torna-se proibitivo. O Instance Store oferece performance de I/O crua imbatível, essencial para lidar com picos de tráfego (spikes) sem sofrer throttling.
    

### 3.2 Estratégias de Armazenamento e o Problema do "Burst Balance"

Ao utilizar EBS, especialmente volumes de uso geral (`gp2`), um problema comum e insidioso é o esgotamento do _Burst Balance_.

- **O Mecanismo:** Volumes `gp2` operam com um sistema de créditos de I/O. Eles acumulam créditos quando ocioso e gastam em picos. Se o Kafka sustenta uma carga de escrita alta por tempo prolongado, os créditos acabam e o volume é estrangulado para sua performance base (que pode ser muito baixa em volumes pequenos). Isso causa um aumento súbito na latência de "produce" e pode derrubar o cluster.
    
- **Solução:** Migrar para volumes `gp3`, que oferecem uma baseline de performance configurável independente da capacidade, ou `io2 Block Express` para latência determinística. Em EC2 autogerenciado, o monitoramento da métrica `BurstBalance` no CloudWatch é obrigatório para volumes legados.
    

### 3.3 Configuração do Sistema Operacional (User Data e Kernel)

A responsabilidade de tunar o Linux recai sobre o operador.

- **Filesystem:** Recomenda-se XFS em vez de EXT4 para diretórios de dados do Kafka, devido à sua melhor gestão de grandes volumes de arquivos e alocação de blocos.
    
- **Swap:** A "swappiness" deve ser configurada próxima a zero (`vm.swappiness=1`). O Kafka prefere que o Linux mate o processo (OOM Killer) a sofrer com a latência de paging em disco.
    
- **Networking:** Ajustes em `net.core.wmem_max` e `net.core.rmem_max` são necessários para saturar links de 10Gbps+ disponíveis em instâncias modernas.
    

---

## 4. Kafka no Amazon EKS: Orquestração Cloud Native

Executar Kafka no Kubernetes (EKS) é a convergência das operações de dados com as práticas modernas de DevOps. No entanto, o Kubernetes foi projetado para aplicações _stateless_, e forçá-lo a gerenciar estado requer ferramentas especializadas chamadas _Operadores_.

### 4.1 O Ecossistema de Operadores: Strimzi e Além

Gerenciar StatefulSets, Services, ConfigMaps e Secrets manualmente para Kafka é propenso a erro humano. O projeto **Strimzi** é o padrão de fato para Kafka no Kubernetes.

- **Automação Declarativa:** O Strimzi introduz Custom Resource Definitions (CRDs) como `Kafka`, `KafkaTopic`, `KafkaUser`. O operador vigia esses recursos e reconcilia o estado do cluster. Se um nó trava, o Strimzi orquestra a substituição do pod, remontagem de volumes e verificação de saúde.
    
- **Rolling Updates:** Atualizar a versão do Kafka ou a configuração dos brokers no EC2 puro é uma operação delicada. No EKS com Strimzi, isso é automatizado. O operador faz o "cordon" de um pod, drena suas conexões, reinicia com a nova imagem e aguarda ele voltar ao ISR antes de prosseguir para o próximo, garantindo disponibilidade zero-downtime.
    

### 4.2 Desafios de Armazenamento Persistente (CSI e PVs)

A integração do EKS com o EBS é feita via Container Storage Interface (CSI).

- **Afinidade Zonal:** Um volume EBS criado na AZ `us-east-1a` não pode ser montado em um nó na `us-east-1b`. Isso impõe uma restrição rígida ao agendador do Kubernetes. Os pods de Kafka devem ter afinidade de nó (`nodeAffinity`) configurada para garantir que, se um pod morrer, ele seja reagendado na mesma AZ onde seu disco reside. O Strimzi facilita isso através de configurações de `rack` no CRD, que propagam labels de topologia para os pods.
    

### 4.3 Exposição e Redes no EKS

Expor o Kafka para fora do cluster EKS é notoriamente complexo devido à necessidade de roteamento direto para brokers individuais.

- **Padrão NLB-per-Broker:** Cria-se um Service do tipo `LoadBalancer` para cada broker. O AWS Load Balancer Controller provisiona um NLB para cada um. É caro e consome muitos recursos.
    
- **Ingress/Route:** Utilizar um Ingress Controller (como NGINX ou AWS ALB) requer suporte a TCP passthrough e configuração cuidadosa de SNI (Server Name Indication) para rotear o tráfego TLS para o broker correto baseado no hostname.
    
- **Advertised Listeners:** O Kafka precisa anunciar seu endereço alcançável. No EKS, isso exige scripts de inicialização que consultem a API do K8s ou metadados da AWS para descobrir o endereço externo do Load Balancer e injetá-lo no `server.properties` em tempo de execução.
    

---

## 5. Cenários de Replicação, Durabilidade e Estratégias Multi-AZ

A resiliência do Kafka não é mágica; é o resultado de uma configuração rigorosa que alinha a lógica de replicação do software com a topologia física da AWS.

### 5.1 Fator de Replicação (RF) e Quórum

A configuração fundamental de durabilidade é o Fator de Replicação (`replication.factor`).

- **RF=1:** Sem redundância. A perda do broker ou disco significa perda de dados. Aceitável apenas para ambientes de desenvolvimento efêmeros.
    
- **RF=2:** Permite tolerar a falha de 1 broker. No entanto, se um broker falha, a partição fica com apenas 1 réplica. Se `min.insync.replicas=2` estiver configurado (para garantir durabilidade estrita), o cluster rejeitará escritas (disponibilidade comprometida).
    
- **RF=3 (Padrão de Produção):** Permite tolerar 1 falha mantendo 2 réplicas ativas, satisfazendo `min.insync.replicas=2` e permitindo a continuidade das escritas. É o padrão ouro para produção na AWS.
    

### 5.2 Rack Awareness: A Chave para Multi-AZ

Sem configuração explícita, o Kafka desconhece a topologia da AWS. Ele pode alocar todas as 3 réplicas de uma partição em brokers que, por acaso, estão na mesma AZ. Se essa AZ falhar, os dados ficam indisponíveis.

- **Mecanismo:** A funcionalidade de _Rack Awareness_ mapeia o conceito lógico de "Rack" do Kafka para a AZ física da AWS.
    
    - **No EC2/MSK:** O parâmetro `broker.rack` é configurado com o ID da AZ (ex: `us-east-1a`). O controlador do Kafka garante que as réplicas de uma partição sejam espalhadas por racks diferentes.
        
    - **No EKS (Strimzi):** Utiliza-se a configuração `topologyKey: topology.kubernetes.io/zone`. O operador lê a label do nó K8s e configura o broker automaticamente.
        

### 5.3 O Impacto na Latência de Escrita

Replicar dados entre AZs (RF=3, Multi-AZ) introduz latência física. O _Round Trip Time_ (RTT) entre AZs varia de 0.5ms a 2ms.

Com `acks=all`, o produtor espera a confirmação de todas as réplicas síncronas.

- **Cálculo:** Latência de Produce = Latência de Disco do Líder + Max(Latência de Rede para Réplica X + Latência de Disco da Réplica X).
    
- Em cenários de alto throughput, essa latência extra reduz a taxa máxima de requisições por segundo. Produtores devem ser tunados com `batch.size` maior e `linger.ms` ajustado para enviar menos pacotes maiores, amortizando o custo da latência de rede.
    

---

## 6. O "Assassino Silencioso": Engenharia de Custos de Transferência de Dados

Um dos aspectos mais críticos e perigosos do Kafka na AWS é o custo de _Data Transfer_. A AWS cobra pelo tráfego que cruza limites de AZ ($0.01/GB de saída + $0.01/GB de entrada = $0.02/GB efetivo em US-East-1, variando por região).

### 6.1 A Matemática da Replicação Cross-AZ

Considere um cluster distribuído em 3 AZs com RF=3. Um produtor externo envia 1TB de dados.

1. **Ingestão:** O líder recebe 1TB. (Se o produtor estiver em outra AZ, +custo).
    
2. **Replicação:** O líder deve enviar cópia para 2 seguidores em outras AZs.
    
    - Líder -> Seguidor 1 (Outra AZ): 1TB transferido. Custo de saída na AZ do líder + entrada na AZ do seguidor.
        
    - Líder -> Seguidor 2 (Outra AZ): 1TB transferido. Custo similar.
        
3. **Resultado:** Para cada 1TB ingerido, gera-se _no mínimo_ 2TB de tráfego Cross-AZ apenas para replicação interna. Isso triplica o custo de rede efetivo da ingestão.
    

### 6.2 Otimização: Rack Awareness para Consumidores (Follower Fetching)

Tradicionalmente, consumidores Kafka liam apenas do líder. Se o consumidor está na AZ-A e o líder na AZ-B, há custo de tráfego Cross-AZ. Em arquiteturas de "Fan-out" (muitos consumidores lendo o mesmo dado), isso multiplica o custo exponencialmente.

Desde o Kafka 2.4 (e suportado no MSK), existe a capacidade de **Leitura da Réplica Mais Próxima** (_Closest Replica Fetching_).

- **Como Funciona:**
    
    1. O broker é configurado com `broker.rack`.
        
    2. O cliente consumidor é configurado com `client.rack` (identificando sua AZ atual).
        
    3. O Kafka permite que o consumidor leia de um _seguidor_ que esteja na mesma AZ, em vez de ir obrigatoriamente ao líder em outra AZ.
        
- **Impacto Financeiro:** Elimina 100% do tráfego de leitura Cross-AZ se consumidores e brokers estiverem co-localizados nas mesmas AZs. Isso pode reduzir a fatura de transferência de dados em 60-80% em clusters de leitura intensiva.
    

### 6.3 Tabela Comparativa de Custos de Rede

|**Tipo de Tráfego**|**Sem Otimização**|**Com Rack Awareness (Consumidor)**|**Economia Potencial**|
|---|---|---|---|
|**Produtor -> Broker**|Alto (aleatório entre AZs)|Médio (se usar particionamento inteligente)|Baixa|
|**Replicação Interna**|Fixo (imposto pelo RF=3)|Fixo (imposto pelo RF=3)|Nenhuma|
|**Broker -> Consumidor**|Altíssimo (Cross-AZ frequente)|**Zero** (Leitura local intra-AZ)|**Extrema**|
|**Total Estimado (1PB)**|~$40,000+|~$12,000+|~70%|

---

## 7. Cenários Detalhados: Do Simples ao Complexo

A seguir, detalhamos configurações de referência para diferentes níveis de maturidade e criticidade.

### 7.1 Cenário 1: Desenvolvimento e Testes (Baixo Custo)

- **Objetivo:** Validação funcional, custo mínimo. Tolerância a perda de dados.
    
- **Infraestrutura:**
    
    - **MSK:** Cluster Single-AZ (não recomendado para produção, mas possível) ou MSK Serverless.
        
    - **EC2:** Instância única (ex: `t3.medium`) rodando Kafka + Zookeeper em Docker via docker-compose.
        
- **Configuração:**
    
    - `replication.factor=1`
        
    - `acks=1` (Líder confirma gravação localmente)
        
    - Storage: EBS gp3 pequeno (50GB).
        
- **Risco:** Se a instância falhar, o cluster para. Se o disco falhar, dados somem.
    

### 7.2 Cenário 2: Produção Padrão (Alta Disponibilidade)

- **Objetivo:** Resiliência a falha de hardware, continuidade de negócio.
    
- **Infraestrutura:**
    
    - **MSK:** Cluster Provisionado em 2 AZs (mínimo) ou 3 AZs (ideal). Instâncias `m5.large` ou `m7g.large`.
        
    - **EKS:** Strimzi Cluster com 3 réplicas de Pod, espalhadas por nós em 3 AZs.
        
- **Configuração:**
    
    - `replication.factor=3`
        
    - `min.insync.replicas=2`
        
    - `acks=all`
        
    - `unclean.leader.election.enable=false` (Prioriza consistência sobre disponibilidade em falhas catastróficas).
        
- **Análise:** Com 3 AZs, você pode perder uma AZ inteira e o cluster continua operando (2 réplicas restantes satisfazem o min.insync=2).
    

### 7.3 Cenário 3: Missão Crítica de Alta Escala e Baixa Latência (Complexo)

- **Objetivo:** Throughput massivo (>1GB/s), latência sub-10ms, retenção infinita, custo otimizado.
    
- **Infraestrutura:**
    
    - **Plataforma:** EC2 Autogerenciado com instâncias `i3en.6xlarge` (NVMe massivo) OU MSK com Tiered Storage ativado.
        
    - **Topologia:** 3 AZs. Uso de _Cluster Placement Groups_ (Partition strategy) para reduzir latência intra-grupo de brokers.
        
- **Otimização de Armazenamento:**
    
    - Se EC2: RAID 0 via software (mdadm) nos discos NVMe para somar IOPS e throughput.
        
    - Configuração de Tiered Storage: Dados quentes no NVMe (ex: últimas 6 horas), dados frios no S3 (via plugin Tiered Storage da Uber ou Confluent, ou nativo no MSK).
        
- **Otimização de Rede:**
    
    - Compressão `zstd` ou `lz4` habilitada nos produtores (reduz payload de rede e disco).
        
    - **Rack Awareness Total:** Brokers configurados com `broker.rack`. Clientes (Produtores e Consumidores) configurados com `client.rack` e lógica de particionamento ciente de localidade se possível.
        
    - **PrivateLink:** Apenas para consumidores externos essenciais; tráfego interno via VPC Peering para evitar custo de processamento de dados do endpoint.
        

---

## 8. Excelência Operacional e Observabilidade

Operar Kafka "no escuro" é impossível. A observabilidade deve focar nos sinais vitais que indicam saturação antes da falha.

### 8.1 Métricas Críticas (CloudWatch / Prometheus)

- **UnderReplicatedPartitions:** Deve ser SEMPRE zero. Qualquer valor positivo indica que réplicas caíram ou não estão conseguindo acompanhar o líder. É o principal alerta de saúde.
    
- **OfflinePartitions:** Crítico. Significa que partições estão sem líder e indisponíveis para escrita/leitura. Perda de serviço.
    
- **NetworkProcessorAvgIdlePercent:** Indica a saturação da CPU do Kafka nas threads de rede. Se cair abaixo de 30% consistentemente, os brokers estão sobrecarregados de requisições e o cluster precisa escalar (mais brokers ou CPU maior).
    
- **DiskUsage:** Monitorar enchimento de disco. No Kafka, disco cheio em um broker faz ele parar de funcionar, o que pode causar efeito cascata (réplicas movendo para outros brokers e enchendo-os também).
    
- **BurstBalance (EBS):** Como discutido, alerta crítico se usar volumes `gp2`.
    

### 8.2 Migração e Recuperação de Desastres

Para cenários de migração ou DR Multi-Região, ferramentas como **MirrorMaker 2** ou **MSK Replicator** são usadas.

- **MSK Replicator:** Serviço totalmente gerenciado para replicar dados entre clusters MSK (mesma ou diferente região). Ele gerencia offsets, tópicos e configurações automaticamente, mas introduz custo adicional por GB replicado.
    
- **Arquitetura Ativo-Passivo:** Um cluster na Região A recebe escritas. O Replicator copia para Região B. Em desastre na A, vira-se a chave (DNS ou configuração de app) para consumidores e produtores apontarem para B. A perda de dados (RPO) é igual à latência de replicação do Replicator (segundos a minutos).
    

## 9. Conclusão

A arquitetura do Apache Kafka na AWS é um exercício de compensação entre variáveis físicas e financeiras. O **Amazon MSK** emergiu como a escolha pragmática para a vasta maioria das empresas, oferecendo um equilíbrio excelente entre facilidade de gestão, segurança e funcionalidades avançadas como Tiered Storage, que fundamentalmente alteram a economia de retenção de dados.

Para cenários onde o MSK não atende — seja por necessidade de hardware exótico (`i3en`), controle granular de sistema operacional ou integração profunda com Kubernetes — as opções de **EC2 Autogerenciado** e **EKS com Strimzi** oferecem poder total, ao custo de uma complexidade operacional que exige equipes de engenharia de dados sênior e dedicadas.

Independentemente da escolha, o sucesso reside nos detalhes da implementação: a configuração correta de **Rack Awareness** para mitigar custos de rede, a seleção criteriosa de tipos de armazenamento para garantir durabilidade sem estourar o orçamento, e uma cultura de observabilidade proativa que monitore não apenas se o cluster está "up", mas se está operando dentro dos parâmetros de eficiência que a nuvem exige. Ignorar esses fatores transforma o Kafka de um ativo estratégico em um dreno financeiro e operacional.