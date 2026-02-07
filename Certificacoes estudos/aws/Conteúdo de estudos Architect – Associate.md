### 1. Design de Arquiteturas Seguras (30%) - O mais importante

Este é o tópico com maior peso. Você precisa dominar como proteger os dados e a rede.

- **IAM (Identity & Access Management):** Políticas de "menor privilégio", Roles (funções) para instâncias EC2 e Lambda, Federação de usuários.
    
- **VPC (Virtual Private Cloud):** Subnets públicas e privadas, Security Groups (Stateful) vs NACLs (Stateless), VPC Peering e Endpoints (Gateway vs Interface).
    
- **Criptografia:** AWS KMS (gerenciamento de chaves), criptografia em repouso (EBS, S3, RDS) e em trânsito (TLS/SSL).
    

### 2. Design de Arquiteturas Resilientes (26%)

Focado em garantir que o sistema não caia ou se recupere sozinho.

- **Alta Disponibilidade:** Multi-AZ (RDS, subnets), ELB (Application vs Network Load Balancer).
    
- **Storage Resiliente:** S3 (Durabilidade de 11 nove), EBS (tipos de volumes e snapshots), EFS (sistema de arquivos para múltiplas instâncias).
    
- **Desacoplamento:** SNS (pub/sub), SQS (filas, visibilidade, Dead Letter Queues). É o coração dos microsserviços.
    

### 3. Design de Arquiteturas de Alta Performance (24%)

Como fazer o sistema ser rápido e escalar.

- **Compute:** Auto Scaling Groups (políticas de escala), tipos de instâncias EC2.
    
- **Bancos de Dados:** RDS Read Replicas (leitura), DynamoDB (performance em milissegundos, DAX), ElastiCache (Redis/Memcached).
    
- **Entrega Global:** CloudFront (CDN), Route 53 (políticas de roteamento: Geoproximidade, Latência, Failover), Global Accelerator.
    

### 4. Design de Arquiteturas Otimizadas para Custo (20%)

Como não gastar mais do que o necessário (vital para quem tem uma software factory como a **ASI**).

- **S3 Tiers:** Saber quando usar Standard, IA, One-Zone ou Glacier.
    
- **Modelos de Compra:** On-demand vs Reserved Instances vs Savings Plans vs **Spot Instances** (muito cobrado para cargas de trabalho tolerantes a falhas).
    
- **Monitoramento:** CloudWatch (métricas) e AWS Budgets.
    

---

### Os "Confrontos" Clássicos da Prova

A AWS ama colocar dois serviços parecidos para você escolher o melhor. Você deve saber a diferença entre:

- **S3 vs EFS vs FSx:** Qual usar para arquivos?
    
- **RDS vs DynamoDB vs Aurora:** Qual usar para o banco?
    
- **SNS vs SQS:** Qual usar para integrar os serviços?
    
- **NAT Gateway vs NAT Instance:** Qual é mais moderno e escalável?
    

> **Dica de Ouro:** Muita gente foca só em EC2 e RDS, mas a prova atual está muito voltada para **Serverless** (Lambda, Step Functions, EventBridge). Como você já usa Java e Spring Boot, entender como integrar esses códigos no Lambda é um diferencial enorme.