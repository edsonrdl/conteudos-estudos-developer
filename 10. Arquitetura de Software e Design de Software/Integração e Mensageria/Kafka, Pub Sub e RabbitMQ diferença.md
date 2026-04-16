### 1. RabbitMQ: Máquina de Estado em Memória (Roteamento e Tarefas)

O RabbitMQ, escrito em Erlang/OTP, foi desenhado para resolver um problema específico: **desacoplamento síncrono com lógica de roteamento complexa na camada de infraestrutura.** Ele age como um sistema de alocação de tarefas (_Task Queue_).

**Como resolve o problema:** A arquitetura do RabbitMQ obriga o produtor a enviar a mensagem para uma _Exchange_, e não para a fila final. O problema que isso resolve é o acoplamento: o serviço que produz o dado não faz ideia de quem vai consumir, ele apenas emite um comando com uma chave de roteamento (ex: `venda.aprovada`). O broker usa ciclos de CPU para aplicar regex ou _hash matching_ e clonar a referência dessa mensagem em memória para as filas N que se registraram para ouvi-la.

**A Decisão Arquitetural e Suas Perdas:** Você escolhe o RabbitMQ quando sua arquitetura exige **Work Queues** (distribuição de carga entre _workers_ concorrentes) ou roteamento granular direto no broker. A vantagem de adotar esse modelo é a entrega com latência de sub-milisegundos, pois o broker tenta entregar a mensagem a partir da memória RAM antes mesmo de tocar o disco (a menos que a memória encha e ocorra o _paging_). A perda intrínseca dessa escolha é a durabilidade de longo prazo e a escalabilidade horizontal. Para escalar o RabbitMQ com segurança de dados, você precisa usar _Quorum Queues_ (baseadas no algoritmo Raft), o que gera um overhead massivo de rede entre os nós do cluster Erlang para replicar o estado da fila a cada ACK recebido. Se o consumidor processar e confirmar, o ponteiro é deletado. Não há como reconstruir o estado do sistema a partir de um RabbitMQ.

---

### 2. Apache Kafka: Commit Log Distribuído (Event Sourcing e Alta Vazão)

O Kafka foi criado no LinkedIn para resolver um problema de O(N²): **integração caótica de dados em larga escala e reconstrução de estado.** Ele não é uma fila, é um banco de dados _append-only_ (apenas inserção) otimizado para acesso sequencial.

**Como resolve o problema:** Em vez de usar a memória RAM do broker (Heap da JVM) e ciclos de CPU para gerenciar roteamento, o Kafka delega isso para o Sistema Operacional. Ele usa o _PageCache_ do Linux e a chamada de sistema `sendfile()` (_Zero-Copy_). O dado vai da placa de rede direto para o disco de forma sequencial, e do disco para o consumidor sem passar pelo espaço de usuário da aplicação. É isso que permite ao Kafka ingerir milhões de eventos por segundo. O problema cirúrgico que ele resolve é o **Event Sourcing**: como os dados são imutáveis e ficam no disco, um novo microsserviço pode apontar seu _offset_ (ponteiro de leitura) para a posição zero e reconstruir todo o histórico de transações do zero.

**A Decisão Arquitetural e Suas Perdas:** Você adota o Kafka quando o sistema precisa atuar como o **Nervo Central (Single Source of Truth)** da empresa, conectando bancos de dados, data lakes e microsserviços sob o paradigma de eventos imutáveis. A vantagem é o _throughput_ extremo I/O-bound e o _time-travel_ dos dados. A perda dessa abordagem recai sobre a complexidade e o acoplamento no código cliente. Como o Kafka é um "broker burro", o seu serviço consumidor precisa embarcar bibliotecas complexas para lidar com rebalanceamento de partições, controle manual de _offsets_ em falhas e filtragem de dados em memória, pois o Kafka obriga o consumidor a ler tudo daquela partição para então descartar o que não serve via código.

---

### 3. Google Cloud Pub/Sub: Barramento de Ingestão de Borda (Escala Global)

O Pub/Sub resolve o problema do **gerenciamento de infraestrutura em picos imprevisíveis de tráfego distribuído.** Ele não roda em instâncias isoladas que você possa tunar; ele roda em cima do Colossus (file system global do Google) e da rede de borda da nuvem.

**Como resolve o problema:** Ele separa as camadas de roteamento e armazenamento físico em datacenters diferentes do Google. Quando você publica uma mensagem, o Pub/Sub grava a mensagem de forma síncrona em múltiplas zonas de disponibilidade antes de retornar o status HTTP 200 para o produtor. Ele resolve o gargalo de provisionamento: se o seu sistema passar de 10 mensagens por segundo para 10.000.000 em um minuto, o Pub/Sub absorve a carga distribuindo o I/O dinamicamente, sem necessidade de configurar partições ou escalar _pods_.

**A Decisão Arquitetural e Suas Perdas:** Você adota o Pub/Sub em arquiteturas _Serverless_ ou quando o foco é a ingestão de dados massiva (telemetria, logs, clickstreams) para alimentar pipelines de Analytics (Dataflow/BigQuery), onde a equipe de engenharia é pequena e não pode se dar ao luxo de gerenciar nós do Zookeeper/KRaft (Kafka). O preço pago por essa solução é a latência base. Onde RabbitMQ e Kafka operam na casa dos milissegundos sobre TCP/AMQP, o Pub/Sub trabalha majoritariamente sobre gRPC/HTTP e arquitetura global. Ele vai introduzir latências maiores na comunicação ponto-a-ponto entre microsserviços. Além disso, a ordenação de mensagens, embora exista via _Ordering Keys_, introduz gargalos artificiais na vazão (limitando a 1MB/s por chave), contrariando a premissa de throughput infinito da ferramenta.

---

### Resumo do Cenário de Decisão

- Se o problema é **rotear comandos** entre microsserviços em tempo real e distribuir tarefas onde a mensagem "morre" após o sucesso (ex: processamento de pagamentos síncronos, envio de emails): A solução arquitetural é o **RabbitMQ**.
    
- Se o problema é **garantir a fonte da verdade**, permitindo que múltiplos sistemas analisem o histórico completo de mutações do sistema em alta velocidade (ex: detecção de fraudes em tempo real, CDC de bancos de dados): A solução arquitetural é o **Kafka**.
    
- Se o problema é **ingerir cargas massivas de dados na borda da internet**, onde escalar infraestrutura própria seria um pesadelo operacional (ex: telemetria de dispositivos IoT global, integração com Data Lakes): A solução arquitetural é o **Pub/Sub**.