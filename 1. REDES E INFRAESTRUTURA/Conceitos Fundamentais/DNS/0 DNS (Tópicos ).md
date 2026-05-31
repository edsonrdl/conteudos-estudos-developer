# 📚 Guia de Estudos: DNS
#tag #redes #infraestrutura #dns

> [!info] Visão Geral
> O DNS (Domain Name System) é o sistema distribuído que traduz nomes de domínio em endereços IP, funcionando como a "lista telefônica da internet". Este guia cobre desde os fundamentos e hierarquia de servidores até os tipos de registros, o processo de resolução e os aspectos de segurança que todo desenvolvedor e engenheiro de infraestrutura precisa conhecer.

---

## 1 Domínio: Fundamentos do DNS

### 1.1 O que é DNS
- **1.1.1. Definição e Propósito**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/1. Fundamentos do DNS/1.1 O que é DNS/1.1.1. Definição e Propósito/1.1.1.1. O que é DNS, como funciona a resolução de nomes e a analogia da lista telefônica da internet|1.1.1.1. O que é DNS, como funciona a resolução de nomes e a analogia da lista telefônica da internet]]

### 1.2 Hierarquia do DNS
- **1.2.1. Servidores Raiz e TLDs**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/1. Fundamentos do DNS/1.2 Hierarquia do DNS/1.2.1. Servidores Raiz e TLDs/1.2.1.1. Servidores raiz, TLDs (Top-Level Domains) e a hierarquia de delegação de nomes|1.2.1.1. Servidores raiz, TLDs (Top-Level Domains) e a hierarquia de delegação de nomes]]

---

## 2 Domínio: Tipos de Registros DNS

### 2.1 Registros Fundamentais
- **2.1.1. A e AAAA**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/2. Tipos de Registros DNS/2.1 Registros Fundamentais/2.1.1. A e AAAA/2.1.1.1. Registro A (IPv4) e AAAA (IPv6) — mapeamento de nome para endereço IP|2.1.1.1. Registro A (IPv4) e AAAA (IPv6) — mapeamento de nome para endereço IP]]
- **2.1.2. CNAME**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/2. Tipos de Registros DNS/2.1 Registros Fundamentais/2.1.2. CNAME/2.1.2.1. Registro CNAME — alias de um nome para outro nome|2.1.2.1. Registro CNAME — alias de um nome para outro nome]]
- **2.1.3. MX**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/2. Tipos de Registros DNS/2.1 Registros Fundamentais/2.1.3. MX/2.1.3.1. Registro MX — roteamento de e-mail para servidores de correio|2.1.3.1. Registro MX — roteamento de e-mail para servidores de correio]]
- **2.1.4. TXT**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/2. Tipos de Registros DNS/2.1 Registros Fundamentais/2.1.4. TXT/2.1.4.1. Registro TXT — verificação de domínio, SPF, DKIM e dados arbitrários|2.1.4.1. Registro TXT — verificação de domínio, SPF, DKIM e dados arbitrários]]

### 2.2 Registros de Infraestrutura
- **2.2.1. NS e SOA**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/2. Tipos de Registros DNS/2.2 Registros de Infraestrutura/2.2.1. NS e SOA/2.2.1.1. Registro NS (Name Server) e SOA (Start of Authority) — delegação e autoridade sobre zonas|2.2.1.1. Registro NS (Name Server) e SOA (Start of Authority) — delegação e autoridade sobre zonas]]

---

## 3 Domínio: Resolução de Nomes

### 3.1 Processo de Resolução
- **3.1.1. Resolução Recursiva vs Iterativa**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/3. Resolução de Nomes/3.1 Processo de Resolução/3.1.1. Resolução Recursiva vs Iterativa/3.1.1.1. Como o resolver recursivo percorre a hierarquia para resolver um nome completo|3.1.1.1. Como o resolver recursivo percorre a hierarquia para resolver um nome completo]]
- **3.1.2. Cache DNS e TTL**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/3. Resolução de Nomes/3.1 Processo de Resolução/3.1.2. Cache DNS e TTL/3.1.2.1. Como o cache e o TTL reduzem latência e controlam a propagação de mudanças|3.1.2.1. Como o cache e o TTL reduzem latência e controlam a propagação de mudanças]]

---

## 4 Domínio: Segurança e Casos Práticos

### 4.1 Segurança no DNS
- **4.1.1. Ataques e Mitigações**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/4. Segurança e Casos Práticos/4.1 Segurança no DNS/4.1.1. Ataques e Mitigações/4.1.1.1. DNS Spoofing, DNS Hijacking e DNSSEC — ataques e mecanismos de proteção|4.1.1.1. DNS Spoofing, DNS Hijacking e DNSSEC — ataques e mecanismos de proteção]]

### 4.2 Ferramentas Práticas
- **4.2.1. Consulta e Diagnóstico**
  - [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/DNS/4. Segurança e Casos Práticos/4.2 Ferramentas Práticas/4.2.1. Consulta e Diagnóstico/4.2.1.1. nslookup e dig — consultar registros DNS e diagnosticar problemas de resolução|4.2.1.1. nslookup e dig — consultar registros DNS e diagnosticar problemas de resolução]]

---

> **Links Relacionados:**
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/0 TCP IP (Tópicos )|TCP/IP]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/0 Internet (Tópicos )|Internet]]
