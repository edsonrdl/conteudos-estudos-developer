# 📚 Guia de Estudos: Insomnia (API Client)
#api #ferramentas #rest #grpc #seguranca

> [!info] Visão Geral
> O Insomnia é um cliente de API open-source focado em design e debug de integrações de rede. Muito além de construir requisições HTTP REST, ele atua como uma suíte de testes operacionais que suporta múltiplos protocolos (gRPC, GraphQL, WebSockets) e gere contextos de segurança complexos, como injeção de tokens dinâmicos e *Mutual TLS* (mTLS), operando num ecossistema local-first focado em performance.

---

## 1 Domínio: Estrutura Lógica e Contexto

### 1.1 Workspaces e Collections
- **1.1.1. A Hierarquia de Organização**
  - 1.1.1.1. **Design Document vs Request Collection:** O Insomnia divide-se em duas abordagens. A *Collection* é usada para debug puro (criar pastas e disparar requests). O *Design Document* usa uma abordagem API-First, onde você escreve uma especificação OpenAPI (Swagger) em YAML e o Insomnia gera a interface de testes automaticamente.
  - 1.1.1.2. **Folders (Pastas):** Não são apenas organizadores visuais. Pastas herdam variáveis de ambiente e configurações de autenticação. Numa arquitetura limpa, você aplica a autenticação na Pasta (ex: "Módulo Vendas"), e todos os requests internos herdam o Token sem você precisar duplicar código.

---

## 2 Domínio: Construção de Requisições e Parâmetros

### 2.1 Passagem de Dados (Under the Hood)
- **2.1.1. Query Parameters vs Path Variables**
  - 2.1.1.1. **Query Params (`?id=1&sort=asc`):** Ficam na aba "Query". O Insomnia faz o *URL encoding* automático (transforma espaços em `%20`). São usados para filtragem, paginação e ordenação no back-end.
  - 2.1.1.2. **Path Variables (`/users/123`):** Inseridos diretamente na URL de topo. No Insomnia, se você usar ambientes dinâmicos, pode substituir o `123` por uma variável como `{{ user_id }}`.
- **2.1.2. O Corpo da Requisição (Payloads)**
  - 2.1.2.1. **JSON (`application/json`):** O formato padrão para APIs modernas. O Insomnia possui validação de sintaxe e formatação automática embutida.
  - 2.1.2.2. **Multipart Form (`multipart/form-data`):** Usado essencialmente para envio de arquivos. **Mecânica Interna:** O Insomnia gera um delimitador criptográfico dinâmico (Boundary) para separar os campos de texto do stream binário do arquivo na mesma requisição TCP, algo impossível de simular com JSON simples.
  - 2.1.2.3. **Form URL Encoded (`application/x-www-form-urlencoded`):** Historicamente usado em submissões de formulários HTML tradicionais ou no fluxo de troca de códigos do OAuth 2.0. Os dados viajam no corpo, mas no mesmo formato de uma Query String.

---

## 3 Domínio: Variáveis e Automação de Fluxo

### 3.1 Environments (Ambientes)
- **3.1.1. O Fim do Hardcode**
  - 3.1.1.1. **Mecânica:** Os Environments são arquivos JSON globais que guardam variáveis dinâmicas. Você define o `Base Environment` (variáveis partilhadas, como `timeout_limit: 5000`) e cria sub-ambientes (`Dev`, `QA`, `Prod`) com as suas URLs específicas.
  - 3.1.1.2. **Aplicação Prática:** A sua URL de request nunca deve ser `https://api.empresa.com/users`. Deve ser sempre `{{ base_url }}/users`. Com um clique no canto superior esquerdo, você aponta o mesmo request do servidor local para a produção.
- **3.1.2. Request Chaining (Encadeamento de Requisições)**
  - 3.1.2.1. **O Problema:** Para testar a API de "Criar Pedido", você precisa de um *Bearer Token* válido, o que obrigaria você a ir no request de Login, copiar o token, voltar no request de Pedido e colar no header.
  - 3.1.2.2. **A Solução (Template Tags):** No Insomnia, você vai na variável do ambiente e aperta `CTRL + Espaço`. Digita `Response` e configura a tag para extrair (via JSONPath) um campo específico da resposta de *outro request*.
  - 3.1.2.3. **Resultado:** Ao clicar em "Enviar" no Pedido, o Insomnia verifica se o Token expirou; se sim, dispara o Login em background, intercepta o novo token injetando-o na memória e envia o Pedido. Automação completa do fluxo.

---

## 4 Domínio: Segurança e Autenticação

### 4.1 Autenticação Nativa (Aba Auth)
- **4.1.1. Delegação de Headers**
  - 4.1.1.1. **Bearer Token:** O Insomnia injeta o cabeçalho `Authorization: Bearer <token>` de forma transparente. Combina-se isso com as variáveis dinâmicas (Request Chaining) para tokens temporários (JWTs).
  - 4.1.1.2. **OAuth 2.0:** O Insomnia atua como o *Client Application*. Ele abre uma mini-janela de navegador embutida, processa os redirecionamentos (Redirect URIs) e a troca do *Authorization Code* com PKCE em background, devolvendo apenas o token final pronto a usar. Elimina a dor de cabeça de simular os fluxos OAuth manualmente.

### 4.2 Autenticação Mútua (Mutual TLS / mTLS)
- **4.1.1. Criptografia Bidirecional na Prática**
  - 4.1.1.1. **Conceito Arquitetural:** No TLS comum (HTTPS), apenas o servidor prova quem é (usando um certificado emitido por uma CA pública). No mTLS, o servidor diz: *"Eu sou a API, mas antes de trocarmos qualquer pacote HTTP, exijo que o teu software também me apresente um certificado válido que prova que és um cliente autorizado"*.
  - 4.1.1.2. **A Aplicação Prática:** Comum em Open Banking e integrações governamentais de alta segurança (B2B). Um simples cURL ou uma aba anônima do Chrome falhará com erro de handshake (ex: `ERR_BAD_SSL_CLIENT_AUTH_CERT`).
  - 4.1.1.3. **Configuração no Insomnia:** 1. Vá ao menu `Application` -> `Preferences` -> Aba `Data`.
    2. Clique em `Client Certificates` -> `New Certificate`.
    3. Defina a `Host` que vai exigir a segurança (ex: `api.banco.com`).
    4. Adicione o `CRT File` (sua chave pública/certificado) e o `Key File` (sua chave privada), além da `Passphrase` (senha) do cofre PFX/P12.
    5. **O Motor:** A partir deste momento, quando você disparar um request para esse host, o Insomnia injeta as chaves criptográficas no nível da camada de transporte (Socket TCP), realizando o handshake mTLS antes da camada de aplicação ser iniciada.

---

## 5 Domínio: Protocolos Modernos (Além do REST)

### 5.1 Suporte Multi-Protocolo
- **5.1.1. gRPC**
  - 5.1.1.1. O Insomnia possui suporte nativo para chamadas RPC. Em vez de JSON, trafega binários Protobuf.
  - 5.1.1.2. **Mecânica de Schema:** Você importa o arquivo `.proto` (o contrato do gRPC) para o Insomnia. Ele lê as definições, mapeia os serviços e oferece autocompletar instantâneo para montar o payload binário de saída, suportando *Unary*, *Server Streaming*, *Client Streaming* e *Bidirectional Streaming*.
- **5.1.2. GraphQL**
  - 5.1.2.1. O Insomnia faz a introspecção automática da API. Ao colocar a URL de um servidor GraphQL, ele faz o download do Schema (a "planta-baixa" da base de dados) e ativa a aba de documentação interativa ao lado, permitindo construir *Queries* e *Mutations* explorando a tipagem forte do protocolo.