# 📚 Guia de Estudos: Firewalls
#tag #redes #infraestrutura #segurança #firewall

> [!info] Visão Geral
> Firewall é o sistema que controla o tráfego de rede com base em regras de segurança, atuando como barreira entre redes confiáveis e não confiáveis. Este guia cobre desde os fundamentos e a evolução histórica dos firewalls até os tipos por tecnologia (Packet Filtering, Stateful, NGFW), a lógica de regras e zonas de segurança, e as ferramentas práticas usadas em Linux e em ambientes cloud.

---

## 1 Domínio: Fundamentos de Firewalls

### 1.1 O que é um Firewall
- **1.1.1. Definição e Propósito**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/1. Fundamentos de Firewalls/1.1 O que é um Firewall/1.1.1. Definição e Propósito/1.1.1.1. O que é firewall, como age como barreira de segurança e seu papel na defesa de redes|1.1.1.1. O que é firewall, como age como barreira de segurança e seu papel na defesa de redes]]

### 1.2 Evolução dos Firewalls
- **1.2.1. Gerações de Firewalls**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/1. Fundamentos de Firewalls/1.2 Evolução dos Firewalls/1.2.1. Gerações de Firewalls/1.2.1.1. Das ACLs simples ao NGFW — como os firewalls evoluíram em resposta às ameaças|1.2.1.1. Das ACLs simples ao NGFW — como os firewalls evoluíram em resposta às ameaças]]

---

## 2 Domínio: Tipos de Firewall

### 2.1 Por Tecnologia
- **2.1.1. Packet Filtering**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/2. Tipos de Firewall/2.1 Por Tecnologia/2.1.1. Packet Filtering/2.1.1.1. Packet Filtering — inspeção de cabeçalhos IP e TCP sem rastrear estado da conexão|2.1.1.1. Packet Filtering — inspeção de cabeçalhos IP e TCP sem rastrear estado da conexão]]
- **2.1.2. Stateful Inspection**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/2. Tipos de Firewall/2.1 Por Tecnologia/2.1.2. Stateful Inspection/2.1.2.1. Stateful Inspection — rastreamento do estado das conexões e tabela de estados|2.1.2.1. Stateful Inspection — rastreamento do estado das conexões e tabela de estados]]
- **2.1.3. Application Layer (Proxy)**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/2. Tipos de Firewall/2.1 Por Tecnologia/2.1.3. Application Layer (Proxy)/2.1.3.1. Firewall de camada de aplicação — inspeção profunda de protocolos e proxy de tráfego|2.1.3.1. Firewall de camada de aplicação — inspeção profunda de protocolos e proxy de tráfego]]
- **2.1.4. NGFW**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/2. Tipos de Firewall/2.1 Por Tecnologia/2.1.4. NGFW/2.1.4.1. Next-Generation Firewall — IPS, DPI, controle de aplicações e inteligência de ameaças|2.1.4.1. Next-Generation Firewall — IPS, DPI, controle de aplicações e inteligência de ameaças]]

### 2.2 Por Implantação
- **2.2.1. Hardware, Software e Cloud**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/2. Tipos de Firewall/2.2 Por Implantação/2.2.1. Hardware, Software e Cloud/2.2.1.1. Firewall físico, software (iptables e pfSense) e cloud (Security Groups e WAF) — quando usar cada um|2.2.1.1. Firewall físico, software (iptables e pfSense) e cloud (Security Groups e WAF) — quando usar cada um]]

---

## 3 Domínio: Regras e Políticas

### 3.1 Como as Regras Funcionam
- **3.1.1. ACLs e Order of Rules**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/3. Regras e Políticas/3.1 Como as Regras Funcionam/3.1.1. ACLs e Order of Rules/3.1.1.1. Como as ACLs são processadas em ordem e o impacto do posicionamento das regras|3.1.1.1. Como as ACLs são processadas em ordem e o impacto do posicionamento das regras]]
- **3.1.2. Zonas de Segurança**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/3. Regras e Políticas/3.1 Como as Regras Funcionam/3.1.2. Zonas de Segurança/3.1.2.1. DMZ, LAN, WAN e zonas de segurança — segmentação e política entre zonas|3.1.2.1. DMZ, LAN, WAN e zonas de segurança — segmentação e política entre zonas]]

---

## 4 Domínio: Firewalls na Prática

### 4.1 Ferramentas e Exemplos
- **4.1.1. iptables e nftables**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/4. Firewalls na Prática/4.1 Ferramentas e Exemplos/4.1.1. iptables e nftables/4.1.1.1. iptables e nftables — filtro de pacotes no Linux com exemplos de regras|4.1.1.1. iptables e nftables — filtro de pacotes no Linux com exemplos de regras]]
- **4.1.2. Security Groups na Nuvem**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/4. Firewalls na Prática/4.1 Ferramentas e Exemplos/4.1.2. Security Groups na Nuvem/4.1.2.1. Security Groups (AWS) e NSGs (Azure) — firewall como código na infraestrutura cloud|4.1.2.1. Security Groups (AWS) e NSGs (Azure) — firewall como código na infraestrutura cloud]]

---

> **Links Relacionados:**
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/VPN/0 VPN (Tópicos )|VPN]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/0 DNS (Tópicos )|DNS]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/0 TCP IP (Tópicos )|TCP/IP]]
