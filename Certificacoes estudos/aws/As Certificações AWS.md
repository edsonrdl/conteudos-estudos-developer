# AWS Certified Solutions Architect - Associate (SAA-C03)
## 1. Design de Arquiteturas Seguras (30% da prova)

Neste domínio, a AWS avalia sua capacidade de proteger dados em repouso e em trânsito, além de gerenciar identidades.

- **VPC (Virtual Private Cloud) e Segurança de Rede:**
    
    - **O que estudar:** Subnets públicas vs. privadas, Internet Gateways (IGW), NAT Gateways, Route Tables e VPC Peering.
        
    - **Por baixo do capô:** Entenda a diferença profunda entre _Security Groups_ (SGs) e _Network ACLs_ (NACLs).
        
        - _Analogia:_ O NACL é a guarita do condomínio (avalia quem entra e quem sai da rua, atuando no nível da Subnet). É _stateless_, exigindo regras explícitas de entrada e saída. O SG é a fechadura biométrica da porta do seu apartamento (atua na interface de rede da instância, ENI). É _stateful_; se a entrada for permitida, o tráfego de retorno é automaticamente liberado.
            
    - **Análise de Escolha:** NAT Gateway gerenciado pela AWS oferece alta disponibilidade e escala automaticamente até 45 Gbps, mas tem um custo por hora e por gigabyte processado. Instâncias NAT (NAT Instances) configuradas manualmente em EC2 podem ser mais baratas para tráfego baixo em ambientes de desenvolvimento, mas você perde a alta disponibilidade automática e passa a ser o responsável por gerenciar atualizações do sistema operacional e gargalos de rede.
        
- **IAM (Identity and Access Management):**
    
    - **O que estudar:** Users, Groups, Roles, Policies (JSON) e AWS STS (Security Token Service).
        
    - **Por baixo do capô:** A prova exige o entendimento de delegação de acessos (AssumeRole). Em vez de embutir credenciais em uma aplicação, você atrela uma IAM Role a uma instância EC2. O serviço de metadados da instância (IMDS) rotaciona essas credenciais temporárias silenciosamente.
        
- **KMS (Key Management Service) e Criptografia:**
    
    - **O que estudar:** CMK (Customer Master Keys), Envelope Encryption.
        
    - **Por baixo do capô:** A AWS usa o conceito de _Envelope Encryption_. O KMS não criptografa gigabytes de dados diretamente. Ele gera uma _Data Key_ (Chave de Dados). O KMS usa a _Master Key_ para criptografar essa _Data Key_. Seus dados são criptografados com a _Data Key_ em texto plano, que logo após é descartada da memória, sendo armazenada apenas na sua versão criptografada junto aos dados.
        

## 2. Design de Arquiteturas Resilientes (26% da prova)

O foco aqui é tolerância a falhas, alta disponibilidade e recuperação de desastres (Disaster Recovery).

- **Bancos de Dados Relacionais (Amazon RDS e Aurora):**
    
    - **O que estudar:** Multi-AZ vs. Read Replicas, failover automático, Aurora Serverless.
        
    - **Análise de Escolha:** Configurações Multi-AZ no RDS são estritamente para _Disaster Recovery_ (replicação síncrona em nível de bloco de armazenamento). A instância standby não pode ser lida; se a primária cair, o CNAME do banco muda automaticamente para a secundária. Já as _Read Replicas_ usam replicação assíncrona baseada no motor do banco (ex: binlog do MySQL) e servem para escalar a performance de leitura da sua aplicação, tirando a carga de relatórios do banco primário. A desvantagem da réplica de leitura é o _lag_ de replicação, o que exige que o código da sua aplicação lide com a consistência eventual.
        
- **Armazenamento de Objetos (Amazon S3):**
    
    - **O que estudar:** Versionamento, Cross-Region Replication (CRR), e consistência.
        
    - **Por baixo do capô:** S3 é um sistema de armazenamento distribuído chave-valor. Atualmente, o S3 oferece consistência forte (read-after-write) para todos os PUTs e DELETEs. O versionamento e MFA Delete são recursos cruciais exigidos na prova para proteger contra deleções acidentais ou ataques de ransomware.
        
- **Desacoplamento de Sistemas (SQS e SNS):**
    
    - **O que estudar:** Filas Standard vs. FIFO (First-In-First-Out), visibilidade da mensagem, Fan-out pattern.
        
    - **Exemplo Cotidiano:** Se o sistema de uma loja online processa pedidos de forma síncrona e o banco de dados tem um pico, tudo trava. Ao introduzir o SQS, o frontend joga o pedido na fila e responde rápido ao cliente. O backend consome no seu próprio ritmo. Se a fila precisar manter a ordem exata de transações financeiras e evitar duplicidade no nível de mensageria, usa-se SQS FIFO, que sacrifica um pouco o throughput (limite de transações por segundo) em favor da ordenação rigorosa e entrega _exactly-once_.
        

## 3. Design de Arquiteturas de Alta Performance (24% da prova)

A prova cobra como escalar recursos para atender à demanda de forma elástica.

- **Load Balancing (ELB) e Auto Scaling:**
    
    - **O que estudar:** Application Load Balancer (ALB), Network Load Balancer (NLB) e Launch Templates.
        
    - **Por baixo do capô:** O ALB opera na camada 7 do modelo OSI (HTTP/HTTPS). Ele abre a conexão TCP com o cliente, inspeciona os cabeçalhos HTTP, toma decisões complexas de roteamento (ex: `/api` vai para um grupo de instâncias, `/imagens` para outro) e abre uma nova conexão com o backend. Isso consome um leve processamento extra. O NLB opera na Camada 4 (TCP/UDP). Ele não inspeciona a camada de aplicação; ele atua no roteamento de pacotes brutos. A vantagem do NLB é a capacidade de lidar com milhões de requisições por segundo com latência ultrabaixa e oferecer IPs estáticos por sub-rede, ideal para aplicações legadas ou tráfego de jogos em tempo real.
        
- **Caching e Entrega de Conteúdo:**
    
    - **O que estudar:** Amazon CloudFront (CDN), Amazon ElastiCache (Redis e Memcached), AWS Global Accelerator.
        
    - **Análise de Escolha:** Redis oferece tipos de dados avançados, persistência e alta disponibilidade via replicação. É excelente para sessões de usuários ou placares em tempo real. Memcached é mais simples, voltado puramente para cache de objetos em memória multithread, mas sem replicação nativa. Na prova, se o cenário exigir persistência ou estruturas de dados complexas, a escolha é sempre Redis.
        

## 4. Design de Arquiteturas Otimizadas em Custo (20% da prova)

O arquiteto precisa saber construir não apenas o que funciona, mas o que é financeiramente viável.

- **Classes de Armazenamento do S3:**
    
    - **O que estudar:** S3 Standard, S3 Intelligent-Tiering, S3 Standard-IA (Infrequent Access), S3 Glacier (Instant, Flexible, Deep Archive).
        
    - **Por baixo do capô:** A economia ocorre ao mover dados por um _Lifecycle Policy_. O Standard-IA é mais barato por GB armazenado, mas cobra uma taxa por GB recuperado (lido). Logo, usar Standard-IA para arquivos muito acessados custará mais caro que o Standard comum. O S3 Intelligent-Tiering resolve isso movendo arquivos automaticamente entre camadas baseando-se no padrão de acesso por trás dos panos, sob uma pequena taxa de monitoramento.
        
- **Modelos de Precificação do EC2:**
    
    - **O que estudar:** On-Demand, Reserved Instances (RI), Savings Plans e Spot Instances.
        
    - **Exemplo Cotidiano:** Para um servidor web com tráfego previsível contínuo, Savings Plans reduzem o custo drasticamente com compromisso de 1 a 3 anos. Para processamento de dados assíncrono (como renderização de vídeos ou análise de relatórios em lote), _Spot Instances_ utilizam a capacidade ociosa da AWS com até 90% de desconto. A condição arquitetural é que a aplicação deve ser _stateless_ e tolerante a falhas, pois a AWS pode encerrar a máquina com apenas 2 minutos de aviso.