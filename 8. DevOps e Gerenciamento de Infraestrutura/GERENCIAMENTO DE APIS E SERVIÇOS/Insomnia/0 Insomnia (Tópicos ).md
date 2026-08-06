# 📚 Guia de Estudos: Insomnia
#tag #api #ferramentas #rest #grpc #graphql #seguranca

> [!info] Visão Geral
> O Insomnia é um cliente de API open-source focado em design e debug de integrações de rede. Muito além de construir requisições HTTP REST, ele atua como uma suíte de testes operacionais que suporta múltiplos protocolos (gRPC, GraphQL, WebSockets) e gerencia contextos de segurança complexos, como injeção de tokens dinâmicos e Mutual TLS (mTLS), operando num ecossistema local-first focado em performance.

---

## 1 Domínio: Estrutura Lógica e Contexto

### 1.1 Workspaces e Collections
- **1.1.1. A Hierarquia de Organização**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/1. Estrutura Lógica e Contexto/1.1 Workspaces e Collections/1.1.1. A Hierarquia de Organização/1.1.1.1. Design Document vs Request Collection — duas abordagens de trabalho no Insomnia|1.1.1.1. Design Document vs Request Collection — duas abordagens de trabalho no Insomnia]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/1. Estrutura Lógica e Contexto/1.1 Workspaces e Collections/1.1.1. A Hierarquia de Organização/1.1.1.2. Folders (Pastas) — herança de variáveis e autenticação entre pastas filhas|1.1.1.2. Folders (Pastas) — herança de variáveis e autenticação entre pastas filhas]]

---

## 2 Domínio: Construção de Requisições e Parâmetros

### 2.1 Passagem de Dados
- **2.1.1. Query Parameters vs Path Variables**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/2. Construção de Requisições e Parâmetros/2.1 Passagem de Dados/2.1.1. Query Parameters vs Path Variables/2.1.1.1. Query Parameters — aba Query, URL encoding automático e casos de uso|2.1.1.1. Query Parameters — aba Query, URL encoding automático e casos de uso]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/2. Construção de Requisições e Parâmetros/2.1 Passagem de Dados/2.1.1. Query Parameters vs Path Variables/2.1.1.2. Path Variables — inserção na URL e uso com variáveis dinâmicas|2.1.1.2. Path Variables — inserção na URL e uso com variáveis dinâmicas]]
- **2.1.2. O Corpo da Requisição (Payloads)**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/2. Construção de Requisições e Parâmetros/2.1 Passagem de Dados/2.1.2. O Corpo da Requisição (Payloads)/2.1.2.1. JSON (application-json) — validação de sintaxe e formatação automática|2.1.2.1. JSON (application/json) — validação de sintaxe e formatação automática]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/2. Construção de Requisições e Parâmetros/2.1 Passagem de Dados/2.1.2. O Corpo da Requisição (Payloads)/2.1.2.2. Multipart Form (multipart-form-data) — envio de arquivos e boundary dinâmico|2.1.2.2. Multipart Form (multipart/form-data) — envio de arquivos e boundary dinâmico]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/2. Construção de Requisições e Parâmetros/2.1 Passagem de Dados/2.1.2. O Corpo da Requisição (Payloads)/2.1.2.3. Form URL Encoded — formulários HTML e fluxo OAuth 2.0|2.1.2.3. Form URL Encoded — formulários HTML e fluxo OAuth 2.0]]

---

## 3 Domínio: Variáveis e Automação de Fluxo

### 3.1 Environments
- **3.1.1. O Fim do Hardcode**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/3. Variáveis e Automação de Fluxo/3.1 Environments/3.1.1. O Fim do Hardcode/3.1.1.1. Mecânica dos Environments — Base Environment, sub-ambientes Dev-QA-Prod e troca com um clique|3.1.1.1. Mecânica dos Environments — Base Environment, sub-ambientes Dev/QA/Prod e troca com um clique]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/3. Variáveis e Automação de Fluxo/3.1 Environments/3.1.1. O Fim do Hardcode/3.1.1.2. Aplicação prática — nunca hardcode URLs, sempre base_url|3.1.1.2. Aplicação prática — nunca hardcode URLs, sempre {{ base_url }}]]
- **3.1.2. Request Chaining (Encadeamento de Requisições)**
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/3. Variáveis e Automação de Fluxo/3.1 Environments/3.1.2. Request Chaining/3.1.2.1. O problema — dependência de token entre requisições|3.1.2.1. O problema — dependência de token entre requisições]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/3. Variáveis e Automação de Fluxo/3.1 Environments/3.1.2. Request Chaining/3.1.2.2. A solução com Template Tags — CTRL + Espaço, Response tag e JSONPath|3.1.2.2. A solução com Template Tags — CTRL + Espaço, Response tag e JSONPath]]
  - [x] [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Insomnia/3. Variáveis e Automação de Fluxo/3.1 Environments/3.1.2. Request Chaining/3.1.2.3. Resultado — login automático em background e injeção de token sem intervenção manual|3.1.2.3. Resultado — login automático em background e injeção de token sem intervenção manual]]

---

## 4 Domínio: Segurança e Autenticação

### 4.1 Autenticação Nativa (Aba Auth)
- **4.1.1. Delegação de Headers**
  - [ ] 4.1.1.1. Bearer Token — injeção do header `Authorization: Bearer` e integração com Request Chaining.
  - [ ] 4.1.1.2. OAuth 2.0 — Insomnia como Client Application, PKCE, redirect URI e troca de código em background.

### 4.2 Autenticação Mútua (Mutual TLS / mTLS)
- **4.2.1. Criptografia Bidirecional na Prática**
  - [ ] 4.2.1.1. Conceito arquitetural — diferença entre TLS padrão e mTLS, validação bidirecional de certificados.
  - [ ] 4.2.1.2. Aplicação prática — Open Banking, integrações B2B governamentais e por que cURL falha.
  - [ ] 4.2.1.3. Configuração no Insomnia — Client Certificates, CRT File, Key File, Passphrase e o handshake na camada TCP.

---

## 5 Domínio: Protocolos Modernos (Além do REST)

### 5.1 Suporte Multi-Protocolo
- **5.1.1. gRPC**
  - [ ] 5.1.1.1. gRPC no Insomnia — chamadas RPC com payload Protobuf e importação do arquivo `.proto`.
  - [ ] 5.1.1.2. Mecânica de Schema — leitura do contrato, autocompletar de payload e tipos de streaming (Unary, Server, Client, Bidirectional).
- **5.1.2. GraphQL**
  - [ ] 5.1.2.1. GraphQL no Insomnia — introspecção automática do Schema, documentação interativa, Queries e Mutations.

---

> **Links Relacionados:**
> [[8. DevOps e Gerenciamento de Infraestrutura/GERENCIAMENTO DE APIS E SERVIÇOS/Postman/0 Postman (Tópicos )|Postman]]
> [[1. REDES E INFRAESTRUTURA/Conceitos Fundamentais/Internet/2. Roteamento e Protocolos/2.2 Protocolos de Aplicação/2.2.1. HTTP/0 HTTP (Tópicos )|HTTP]]
