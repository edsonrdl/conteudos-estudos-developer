# 📚 Guia de Estudos: HTTP — HyperText Transfer Protocol
#tag #redes #infraestrutura #internet #http #protocolos

> [!info] Visão Geral
> HTTP (HyperText Transfer Protocol) é o protocolo de comunicação que sustenta a Web. Define como clientes (browsers, apps, CLIs) e servidores trocam mensagens — cada interação é um ciclo de requisição e resposta stateless. Este sub-glossário cobre cada componente com profundidade: URL, métodos, headers de requisição e resposta, tipos de body e status codes — tudo que você precisa para entender, depurar e projetar APIs e sistemas web.

---

## 1 Domínio: Fundamentos

### 1.1 Modelo Request-Response
- **1.1.1. O protocolo**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/1. Fundamentos/1.1 Modelo Request-Response/1.1.1. O protocolo/1.1.1.1. O que é HTTP — modelo request-response, stateless, conexões TCP e papel na web|1.1.1.1. O que é HTTP — modelo request/response, stateless, conexões TCP e papel na web]]

### 1.2 Versões do HTTP
- **1.2.1. Evolução do protocolo**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/1. Fundamentos/1.2 Versões do HTTP/1.2.1. Evolução do protocolo/1.2.1.1. HTTP-1.1 vs HTTP-2 vs HTTP-3 — conexões, multiplexing, header compression e QUIC|1.2.1.1. HTTP/1.1 vs HTTP/2 vs HTTP/3 — conexões, multiplexing, header compression e QUIC]]

---

## 2 Domínio: Estrutura da Requisição

### 2.1 URL e Parâmetros
- **2.1.1. Anatomia da URL**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.1 URL e Parâmetros/2.1.1. Anatomia da URL/2.1.1.1. URL completa — scheme, host, port, path, query string e fragment — cada parte explicada|2.1.1.1. URL completa — scheme, host, port, path, query string e fragment — cada parte explicada]]
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.1 URL e Parâmetros/2.1.1. Anatomia da URL/2.1.1.2. Query Parameters — sintaxe, encoding (percent-encoding), tipos de valores e boas práticas|2.1.1.2. Query Parameters — sintaxe, encoding (percent-encoding), tipos de valores e boas práticas]]

### 2.2 Métodos HTTP
- **2.2.1. Verbos e semântica**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.2 Métodos HTTP/2.2.1. Verbos e semântica/2.2.1.1. GET, POST, PUT, PATCH, DELETE — semântica, idempotência, safe methods e quando usar cada um|2.2.1.1. GET, POST, PUT, PATCH, DELETE — semântica, idempotência, safe methods e quando usar cada um]]
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.2 Métodos HTTP/2.2.1. Verbos e semântica/2.2.1.2. HEAD, OPTIONS, CONNECT e TRACE — métodos auxiliares, CORS preflight e casos de uso|2.2.1.2. HEAD, OPTIONS, CONNECT e TRACE — métodos auxiliares, CORS preflight e casos de uso]]

### 2.3 Request Headers
- **2.3.1. Cabeçalhos de requisição**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.3 Request Headers/2.3.1. Cabeçalhos de requisição/2.3.1.1. Headers de autenticação e autorização — Authorization (Bearer, Basic), Cookie, API-Key patterns|2.3.1.1. Headers de autenticação e autorização — Authorization (Bearer, Basic), Cookie, API-Key patterns]]
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.3 Request Headers/2.3.1. Cabeçalhos de requisição/2.3.1.2. Headers de conteúdo e negociação — Content-Type, Accept, Accept-Encoding, Accept-Language|2.3.1.2. Headers de conteúdo e negociação — Content-Type, Accept, Accept-Encoding, Accept-Language]]
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.3 Request Headers/2.3.1. Cabeçalhos de requisição/2.3.1.3. Headers de cache condicional — Cache-Control, If-Modified-Since, If-None-Match, ETag|2.3.1.3. Headers de cache condicional — Cache-Control, If-Modified-Since, If-None-Match, ETag]]
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.3 Request Headers/2.3.1. Cabeçalhos de requisição/2.3.1.4. Headers de contexto e rastreamento — Origin, Referer, User-Agent, X-Forwarded-For, X-Request-ID|2.3.1.4. Headers de contexto e rastreamento — Origin, Referer, User-Agent, X-Forwarded-For, X-Request-ID]]

### 2.4 Body Types
- **2.4.1. Tipos de corpo de requisição**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.4 Body Types/2.4.1. Tipos de corpo/2.4.1.1. JSON (application-json) — estrutura, serialização, Content-Type e quando usar|2.4.1.1. JSON (application/json) — estrutura, serialização, Content-Type e quando usar]]
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.4 Body Types/2.4.1. Tipos de corpo/2.4.1.2. Form-urlencoded e multipart-form-data — formulários HTML, upload de arquivos e diferenças|2.4.1.2. Form-urlencoded e multipart/form-data — formulários HTML, upload de arquivos e diferenças]]
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/2. Estrutura da Requisição/2.4 Body Types/2.4.1. Tipos de corpo/2.4.1.3. Plain text, XML e binary — outros content-types, casos de uso e comparativo|2.4.1.3. Plain text, XML e binary — outros content-types, casos de uso e comparativo]]

---

## 3 Domínio: Estrutura da Resposta

### 3.1 Status Codes
- **3.1.1. Códigos de status**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/3. Estrutura da Resposta/3.1 Status Codes/3.1.1. Códigos de status/3.1.1.1. Códigos de Status HTTP — 1xx, 2xx, 3xx, 4xx e 5xx — significado, uso correto e erros comuns|3.1.1.1. Códigos de Status HTTP — 1xx, 2xx, 3xx, 4xx e 5xx — significado, uso correto e erros comuns]]

### 3.2 Response Headers
- **3.2.1. Cabeçalhos de resposta**
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/3. Estrutura da Resposta/3.2 Response Headers/3.2.1. Cabeçalhos de resposta/3.2.1.1. Headers de conteúdo — Content-Type, Content-Length, Content-Encoding, Transfer-Encoding|3.2.1.1. Headers de conteúdo — Content-Type, Content-Length, Content-Encoding, Transfer-Encoding]]
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/3. Estrutura da Resposta/3.2 Response Headers/3.2.1. Cabeçalhos de resposta/3.2.1.2. Headers de cache — Cache-Control, ETag, Last-Modified, Expires, Vary|3.2.1.2. Headers de cache — Cache-Control, ETag, Last-Modified, Expires, Vary]]
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/3. Estrutura da Resposta/3.2 Response Headers/3.2.1. Cabeçalhos de resposta/3.2.1.3. Headers de segurança — CORS, CSP, HSTS, X-Frame-Options, X-Content-Type-Options|3.2.1.3. Headers de segurança — CORS, CSP, HSTS, X-Frame-Options, X-Content-Type-Options]]
  - [x] [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/3. Estrutura da Resposta/3.2 Response Headers/3.2.1. Cabeçalhos de resposta/3.2.1.4. Headers de sessão e navegação — Set-Cookie (atributos), Location, WWW-Authenticate|3.2.1.4. Headers de sessão e navegação — Set-Cookie (atributos), Location, WWW-Authenticate]]

---

> **Links Relacionados:**
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.2. HTTPS/0 HTTPS (Tópicos )|HTTPS]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/3. Segurança e Privacidade/3.1 Segurança na Transmissão/3.1.1. TLS e SSL/3.1.1.1. Como o TLS protege o tráfego na Internet — handshake, certificados digitais e infraestrutura de chave pública (PKI)|TLS/SSL]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/TCP IP/0 TCP IP (Tópicos )|TCP/IP]]
