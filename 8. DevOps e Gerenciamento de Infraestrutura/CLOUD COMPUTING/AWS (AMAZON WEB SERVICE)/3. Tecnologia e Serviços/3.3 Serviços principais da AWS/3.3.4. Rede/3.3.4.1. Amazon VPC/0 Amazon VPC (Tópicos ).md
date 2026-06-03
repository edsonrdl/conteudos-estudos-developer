# 📚 Guia de Estudos: Amazon VPC
#tag #aws #cloud #rede #vpc #networking

> [!info] Visão Geral
> Amazon VPC (Virtual Private Cloud) é a fundação de toda arquitetura de rede na AWS. É uma rede virtual isolada onde você lança recursos (EC2, RDS, ECS, Lambda, etc.) com controle total sobre endereçamento IP, roteamento, subnets e segurança. Sem VPC, nenhum recurso computacional pode existir na AWS. Este guia cobre desde o modelo de isolamento, CIDR e a ponte com redes tradicionais (TCP/IP), até segurança com Security Groups e NACLs, conectividade com a internet, com outros serviços AWS, entre VPCs e com ambientes on-premises.

---

## 1 Domínio: Fundamentos e Arquitetura

### 1.1 O que é VPC
- **1.1.1. Conceito e isolamento**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.4. Rede/3.3.4.1. Amazon VPC/1. Fundamentos e Arquitetura/1.1 O que é VPC/1.1.1. Conceito e isolamento/1.1.1.1. O que é VPC — isolamento de rede, relação com regiões e AZs e como difere de rede física tradicional|1.1.1.1. O que é VPC — isolamento de rede, relação com regiões/AZs e como difere de rede física tradicional]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.4. Rede/3.3.4.1. Amazon VPC/1. Fundamentos e Arquitetura/1.1 O que é VPC/1.1.1. Conceito e isolamento/1.1.1.2. VPC Default — o que é, como funciona e por que não usar em produção|1.1.1.2. VPC Default — o que é, como funciona e por que não usar em produção]]

### 1.2 Redes Tradicionais na AWS
- **1.2.1. Da rede física para a VPC**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.4. Rede/3.3.4.1. Amazon VPC/1. Fundamentos e Arquitetura/1.2 Redes Tradicionais na AWS/1.2.1. Da rede física para a VPC/1.2.1.1. Como CIDR, subnetting, RFC 1918 e máscaras de rede se traduzem para a VPC — diferenças e particularidades da AWS|1.2.1.1. Como CIDR, subnetting, RFC 1918 e máscaras de rede se traduzem para a VPC — diferenças e particularidades da AWS]]

### 1.3 CIDR e Endereçamento IP
- **1.3.1. Blocos de endereços na VPC**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.4. Rede/3.3.4.1. Amazon VPC/1. Fundamentos e Arquitetura/1.3 CIDR e Endereçamento IP/1.3.1. Blocos de endereços na VPC/1.3.1.1. CIDR da VPC — faixas RFC 1918 permitidas, tamanhos válidos e os 5 endereços reservados pela AWS por subnet|1.3.1.1. CIDR da VPC — faixas RFC 1918 permitidas, tamanhos válidos e os 5 endereços reservados pela AWS por subnet]]
  - [ ] 1.3.1.2. Planejamento de CIDR — dimensionar para crescimento, evitar sobreposição com on-premises e estratégias multi-VPC.
- **1.3.2. IPv6 na VPC**
  - [ ] 1.3.2.1. IPv6 na VPC — dual-stack, Egress-Only Internet Gateway e diferenças em relação ao IPv4.

---

## 2 Domínio: Subnets

### 2.1 Tipos de Subnet
- **2.1.1. Públicas vs Privadas**
  - [ ] 2.1.1.1. O que realmente diferencia subnet pública de privada — é a route table, não o nome.

### 2.2 Design de Subnets
- **2.2.1. Arquitetura e sizing**
  - [ ] 2.2.1.1. Design multi-AZ — quantas subnets criar, distribuição entre AZs e isolamento por camada (web/app/data).
  - [ ] 2.2.1.2. Subnet sizing — trade-offs entre blocos grandes vs pequenos e impacto em ENIs do ECS/EKS.

---

## 3 Domínio: Roteamento

### 3.1 Route Tables
- **3.1.1. Conceito e funcionamento**
  - [ ] 3.1.1.1. Route Tables — rota local, longest prefix match, main route table e associação com subnets.
  - [ ] 3.1.1.2. Roteamento avançado — Gateway route tables, Ingress routing e propagação de rotas BGP.

---

## 4 Domínio: Gateways e Acesso à Internet

### 4.1 Internet Gateway
- **4.1.1. Acesso bidirecional**
  - [ ] 4.1.1.1. Internet Gateway — como habilita NAT 1:1, Elastic IPs e os requisitos para uma subnet ser pública.

### 4.2 NAT
- **4.2.1. Saída de subnets privadas**
  - [ ] 4.2.1.1. NAT Gateway — arquitetura, alta disponibilidade por AZ, custo por hora e por GB processado.
  - [ ] 4.2.1.2. NAT Gateway vs NAT Instance — break-even de custo, bandwidth e quando cada um faz sentido.

---

## 5 Domínio: Segurança de Rede

### 5.1 Security Groups
- **5.1.1. Firewall stateful por instância**
  - [ ] 5.1.1.1. Security Groups — stateful, regras de ingresso e egresso, referência entre SGs e o default SG.
  - [ ] 5.1.1.2. Boas práticas de Security Groups — menor privilégio, referência entre SGs vs CIDR e troubleshooting.

### 5.2 Network ACLs
- **5.2.1. Firewall stateless por subnet**
  - [ ] 5.2.1.1. NACLs — stateless, regras numeradas, ALLOW e DENY explícitos e ordem de avaliação.
  - [ ] 5.2.1.2. Security Groups vs NACLs — sobreposição de camadas, compliance e padrões de uso combinado.

---

## 6 Domínio: VPC Endpoints

### 6.1 Gateway Endpoints
- **6.1.1. S3 e DynamoDB sem NAT**
  - [ ] 6.1.1.1. Gateway Endpoints — como funcionam, endpoint policy, gratuitos e limitações de escopo.

### 6.2 Interface Endpoints e PrivateLink
- **6.2.1. Acesso privado via ENI**
  - [ ] 6.2.1.1. Interface Endpoints — ENI com IP privado, DNS privado e catálogo de serviços AWS suportados.
  - [ ] 6.2.1.2. AWS PrivateLink — expor serviços entre VPCs de forma privada, segura e sem peering.

---

## 7 Domínio: Conectividade entre VPCs

### 7.1 VPC Peering
- **7.1.1. Conexão ponto a ponto**
  - [ ] 7.1.1.1. VPC Peering — como funciona, limitação não-transitiva, inter-region peering e quando usar.

### 7.2 Transit Gateway
- **7.2.1. Hub central de conectividade**
  - [ ] 7.2.1.1. Transit Gateway — arquitetura hub-and-spoke, route tables do TGW e uso em ambientes multi-conta.
  - [ ] 7.2.1.2. Transit Gateway vs VPC Peering — custo, escala e critério de escolha.

---

## 8 Domínio: Conectividade Híbrida

### 8.1 VPN Site-to-Site
- **8.1.1. Túnel IPsec sobre internet**
  - [ ] 8.1.1.1. Site-to-Site VPN — Virtual Private Gateway, Customer Gateway, túneis redundantes e limites de banda.

### 8.2 Direct Connect
- **8.2.1. Link físico dedicado**
  - [ ] 8.2.1.1. Direct Connect — dedicated vs hosted connection, Virtual Interfaces (VIF) e casos de uso.
  - [ ] 8.2.1.2. Direct Connect Gateway e alta disponibilidade — redundância, failover com VPN e latência garantida.

---

## 9 Domínio: Monitoramento e Diagnóstico

### 9.1 VPC Flow Logs
- **9.1.1. Captura de tráfego**
  - [ ] 9.1.1.1. VPC Flow Logs — campos do log, destinos (CloudWatch/S3/Kinesis), análise de tráfego e custo.

### 9.2 Ferramentas de Diagnóstico
- **9.2.1. Troubleshooting de rede**
  - [ ] 9.2.1.1. Reachability Analyzer e Network Access Analyzer — diagnosticar conectividade sem enviar tráfego real.

---

> **Links Relacionados:**
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/0 TCP IP (Tópicos )|TCP/IP — Fundamentos de Redes]]
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.1. Computação/3.3.1.1. Amazon EC2/0 Amazon EC2 (Tópicos )|Amazon EC2]]
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.4. Rede/3.3.4.5. Amazon Route 53/0 Amazon Route 53 (Tópicos )|Amazon Route 53]]
> [[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/3. Tecnologia e Serviços/3.3 Serviços principais da AWS/3.3.4. Rede/3.3.4.7. Elastic Load Balancing/0 Elastic Load Balancing (Tópicos )|Elastic Load Balancing]]
