# 📚 Guia de Estudos: Proxy
#tag #redes #infraestrutura #proxy #segurança

> [!info] Visão Geral
> Um proxy é um servidor intermediário que atua entre clientes e servidores, podendo inspecionar, cachear, filtrar e rotear tráfego. Este guia cobre os dois tipos fundamentais (forward e reverse proxy), os protocolos SOCKS5 e HTTP, as ferramentas mais usadas em produção (Nginx, HAProxy, Squid), e os aspectos de segurança como anonimato, SSL termination e mTLS.

---

## 1 Domínio: Fundamentos de Proxy

### 1.1 O que é um Proxy
- **1.1.1. Definição e Propósito**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/1. Fundamentos de Proxy/1.1 O que é um Proxy/1.1.1. Definição e Propósito/1.1.1.1. O que é proxy, como age como intermediário e diferença entre proxy e VPN|1.1.1.1. O que é proxy, como age como intermediário e diferença entre proxy e VPN]]

### 1.2 Forward vs Reverse Proxy
- **1.2.1. Direção do Proxy**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/1. Fundamentos de Proxy/1.2 Forward vs Reverse Proxy/1.2.1. Direção do Proxy/1.2.1.1. Forward Proxy (cliente para internet) vs Reverse Proxy (internet para servidor) — casos de uso de cada um|1.2.1.1. Forward Proxy (cliente para internet) vs Reverse Proxy (internet para servidor) — casos de uso de cada um]]

---

## 2 Domínio: Tipos de Proxy

### 2.1 Forward Proxy
- **2.1.1. Proxy Transparente e Explícito**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/2. Tipos de Proxy/2.1 Forward Proxy/2.1.1. Proxy Transparente e Explícito/2.1.1.1. Proxy transparente (intercepta sem configuração no cliente) vs explícito (configurado manualmente)|2.1.1.1. Proxy transparente (intercepta sem configuração no cliente) vs explícito (configurado manualmente)]]
- **2.1.2. SOCKS e HTTP Proxy**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/2. Tipos de Proxy/2.1 Forward Proxy/2.1.2. SOCKS e HTTP Proxy/2.1.2.1. SOCKS5 vs HTTP Proxy — diferenças de protocolo, casos de uso e limitações|2.1.2.1. SOCKS5 vs HTTP Proxy — diferenças de protocolo, casos de uso e limitações]]

### 2.2 Reverse Proxy
- **2.2.1. Load Balancer e API Gateway**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/2. Tipos de Proxy/2.2 Reverse Proxy/2.2.1. Load Balancer e API Gateway/2.2.1.1. Reverse proxy como load balancer, terminação SSL e ponto de entrada único para múltiplos serviços|2.2.1.1. Reverse proxy como load balancer, terminação SSL e ponto de entrada único para múltiplos serviços]]
- **2.2.2. Cache e CDN**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/2. Tipos de Proxy/2.2 Reverse Proxy/2.2.2. Cache e CDN/2.2.2.1. Proxy de cache e CDN — como reduzem latência servindo conteúdo estático próximo ao usuário|2.2.2.1. Proxy de cache e CDN — como reduzem latência servindo conteúdo estático próximo ao usuário]]

---

## 3 Domínio: Ferramentas e Implementações

### 3.1 Ferramentas Populares
- **3.1.1. Nginx e HAProxy**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/3. Ferramentas e Implementações/3.1 Ferramentas Populares/3.1.1. Nginx e HAProxy/3.1.1.1. Nginx e HAProxy como reverse proxy — configuração, balanceamento de carga e terminação TLS|3.1.1.1. Nginx e HAProxy como reverse proxy — configuração, balanceamento de carga e terminação TLS]]
- **3.1.2. Squid**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/3. Ferramentas e Implementações/3.1 Ferramentas Populares/3.1.2. Squid/3.1.2.1. Squid — forward proxy corporativo com cache, controle de acesso e filtragem de conteúdo|3.1.2.1. Squid — forward proxy corporativo com cache, controle de acesso e filtragem de conteúdo]]

---

## 4 Domínio: Segurança e Casos Práticos

### 4.1 Proxy e Segurança
- **4.1.1. Anonimato e Privacidade**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/4. Segurança e Casos Práticos/4.1 Proxy e Segurança/4.1.1. Anonimato e Privacidade/4.1.1.1. Proxy como ferramenta de privacidade — níveis de anonimato elite vs anonymous vs transparent|4.1.1.1. Proxy como ferramenta de privacidade — níveis de anonimato elite vs anonymous vs transparent]]
- **4.1.2. SSL Termination e mTLS**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Proxy/4. Segurança e Casos Práticos/4.1 Proxy e Segurança/4.1.2. SSL Termination e mTLS/4.1.2.1. Terminação SSL no proxy e mTLS — como o reverse proxy gerencia certificados e autenticação mútua|4.1.2.1. Terminação SSL no proxy e mTLS — como o reverse proxy gerencia certificados e autenticação mútua]]

---

> **Links Relacionados:**
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Firewalls/0 Firewalls (Tópicos )|Firewalls]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/VPN/0 VPN (Tópicos )|VPN]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/0 DNS (Tópicos )|DNS]]
