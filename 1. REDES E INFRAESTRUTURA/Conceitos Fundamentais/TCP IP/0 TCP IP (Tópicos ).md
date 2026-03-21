# 📚 Guia de Estudos: TCP/IP
#tag #redes #infraestrutura

> [!info] Visão Geral
> O TCP/IP é a suíte de protocolos fundamental que sustenta a Internet e a maioria das redes corporativas. Este guia mapeia desde a fundação da pilha de protocolos até a matemática de roteamento (CIDR e Subnets) e o comportamento das camadas de transporte (TCP/UDP) e ferramentas de troubleshooting.

---

## 1 Domínio: Fundamentos e Arquitetura

### 1.1 [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/1. Visão Geral|Visão Geral e Histórico]]
- **1.1.1. Propósito do Protocolo**
  - 1.1.1.1. Explicação resumida do TCP/IP, histórico e propósito de interconexão de redes heterogêneas.

### 1.2 [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/2. Como Funciona o TCP IP.md|Como Funciona a Pilha TCP/IP]]
- **1.2.1. Estrutura em Camadas**
  - 1.2.1.1. Explicação da pilha (Aplicação, Transporte, Internet, Enlace/Acesso à Rede).
- **1.2.2. Modelos de Referência**
  - 1.2.2.1. OSI vs TCP/IP (Comparativo entre as 7 camadas conceituais e as 4 camadas práticas).
- **1.2.3. Tráfego de Dados**
  - 1.2.3.1. Encapsulamento (Como os dados descem a pilha ganhando cabeçalhos e sobem a pilha sendo desencapsulados).

---

## 2 Domínio: Endereçamento e Roteamento

### 2.1 [[3. Endereçamento IP|Endereçamento IP]]
- **2.1.1. Classes Tradicionais (A, B, C)**
  - 2.1.1.1. O que são e como dividem a rede dos hosts.
  - 2.1.1.2. Intervalos numéricos de cada classe.
  - 2.1.1.3. Quando usar cada uma (Histórico vs Prática atual).
- **2.1.2. Máscara de Rede**
  - 2.1.2.1. Explicação simples de como ela separa a porção de "Rede" da porção de "Host".
  - 2.1.2.2. Exemplos práticos de aplicação.
- **2.1.3. CIDR (Classless Inter-Domain Routing)**
  - 2.1.3.1. Por que foi criado (esgotamento de IPs e rigidez das Classes).
  - 2.1.3.2. Exemplo prático da notação com barra (ex: `/24`, `/26`).

### 2.2 [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/4. Redes e Sub-redes.md|Redes e Sub-redes]]
- **2.2.1. Arquitetura Lógica**
  - 2.2.1.1. Diferença entre Rede principal e Sub-rede.
- **2.2.2. Sub-redes (Subnets)**
  - 2.2.2.1. Conceito e necessidade de segmentação de broadcast e segurança.
- **2.2.3. Matemática de Redes**
  - 2.2.3.1. Como calcular sub-redes (com exemplos de bits emprestados).
- **2.2.4. VLSM (Variable Length Subnet Mask)**
  - 2.2.4.1. Como otimizar o desperdício de IPs criando sub-redes de tamanhos diferentes dentro do mesmo bloco.

---

## 3 Domínio: Transporte e Comunicação (Layer 4)

### 3.1 [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/5. Protocolos TCP e UDP.md|Protocolos TCP e UDP]]
- **3.1.1. TCP (Transmission Control Protocol)**
  - 3.1.1.1. Confiabilidade, Handshake de 3 vias (Three-way handshake) e ordenação de pacotes.
- **3.1.2. UDP (User Datagram Protocol)**
  - 3.1.2.1. Sem conexão, rápido, melhor esforço (best-effort) e sem garantia de entrega.
- **3.1.3. Análise de Decisão**
  - 3.1.3.1. Diferenças e casos de uso (ex: Streaming/VoIP usa UDP; Transferência de Arquivos/Bancos de Dados usa TCP).

### 3.2 Portas e Sockets
- **3.2.1. Portas Bem Conhecidas (Well-Known Ports)**
  - 3.2.1.1. Mapeamento dos serviços principais (ex: 80 HTTP, 443 HTTPS, 22 SSH).
- **3.2.2. Sockets**
  - 3.2.2.1. Como o SO lida com portas e forma o Socket (IP + Porta) para multiplexar múltiplas conexões de rede.

---

## 4 Domínio: Operação e Troubleshooting

### 4.1 Ferramentas e Exemplos Práticos
- **4.1.1. Rastreamento e Conectividade**
  - 4.1.1.1. **Ping:** Testa alcançabilidade usando ICMP.
  - 4.1.1.2. **TraceRoute:** Mapeia os saltos (routers) até o destino alterando o TTL.
- **4.1.2. Análise Local e Inspeção**
  - 4.1.2.1. **Netstat:** Verifica portas abertas e conexões ativas no Sistema Operacional.
  - 4.1.2.2. **Wireshark:** Sniffer de rede para análise profunda de pacotes (Deep Packet Inspection).

---
> **Links Relacionados:** 
> [[Internet]]
>  [[DNS]]
>  [[NAT]]
>  [[Firewall]]