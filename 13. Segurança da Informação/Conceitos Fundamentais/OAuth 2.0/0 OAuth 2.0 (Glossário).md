# 📚 Guia de Estudos: OAuth 2.0 (Open Authorization)

> [!info] Visão Geral
> O OAuth 2.0 é um framework de **Autorização Delegada**. Ele permite que uma aplicação de terceiros (Client) obtenha acesso limitado a um serviço HTTP (Resource Server) em nome do dono do recurso (Resource Owner), sem que este precise compartilhar as suas credenciais (usuário e senha). É a fundação por trás dos botões "Entrar com o Google" ou "Entrar com o GitHub", separando fisicamente o servidor que valida a senha do servidor que provê os dados da API.

---

## 1 Domínio: Os Atores do Ecossistema (Roles)

### 1.1 Os Quatro Papéis Fundamentais
- **1.1.1. Resource Owner (Dono do Recurso)**
  - 1.1.1.1. **Conceito:** O utilizador final. É a pessoa ou entidade que tem o poder de conceder acesso a uma parte protegida da sua conta.
- **1.1.2. Client (A Aplicação Cliente)**
  - 1.1.2.1. **Conceito:** A aplicação (Front-end, Mobile ou Back-end) que está a pedir acesso aos dados do utilizador. O nome "Client" não se refere ao navegador, mas sim ao software que consome a API.
- **1.1.3. Authorization Server (Servidor de Autorização)**
  - 1.1.3.1. **Conceito:** O servidor responsável por autenticar o utilizador, pedir o seu consentimento e, em caso de sucesso, emitir os Tokens. (Ex: Auth0, Keycloak, AWS Cognito, IdentityServer).
- **1.1.4. Resource Server (Servidor de Recursos)**
  - 1.1.4.1. **Conceito:** A sua API (ex: `api.asi.com/v1/users`). É o servidor que hospeda os dados protegidos e que exige um *Access Token* válido para responder às requisições.

---

## 2 Domínio: Fluxos de Concessão (Grant Types)

### 2.1 Fluxos para Aplicações com Utilizador Interativo
- **2.1.1. Authorization Code Flow (O Padrão Ouro)**
  - 2.1.1.1. **Mecânica:** O Client redireciona o utilizador para o *Authorization Server*. Após o login, o servidor redireciona de volta com um `code` (código temporário). O Back-end do Client pega nesse `code` e troca-o por um `Access Token` numa chamada direta (server-to-server) com o *Authorization Server*.
  - 2.1.1.2. **Segurança:** Extremamente seguro porque o `Access Token` nunca transita no navegador do utilizador; fica retido de forma segura no Back-end.
- **2.1.2. Authorization Code com PKCE (Proof Key for Code Exchange)**
  - 2.1.2.1. **O Problema das SPAs/Mobile:** Aplicações React/Vue (Single Page Applications) e Apps Nativas não conseguem guardar "Segredos de Cliente" (`client_secret`) de forma segura, pois o código fonte é público.
  - 2.1.2.2. **A Solução (PKCE):** Adiciona uma verificação criptográfica dinâmica. A aplicação gera um código secreto aleatório (`code_verifier`) na hora do login, envia o hash dele (`code_challenge`) e, na hora de pedir o Token, prova que foi ela mesma quem iniciou o fluxo enviando o verificador original. **É o padrão obrigatório hoje para Front-end e Mobile.**
- **2.1.3. Implicit Flow (Obsoleto/Inseguro)**
  - 2.1.3.1. **Histórico:** Antigamente usado em SPAs. O *Authorization Server* devolvia o `Access Token` diretamente na URL de redirecionamento. 
  - 2.1.3.2. **Abandono:** Foi descontinuado nas boas práticas modernas porque expõe o Token no histórico do navegador e em logs de proxies. Foi substituído pelo PKCE.

### 2.2 Fluxos Máquina-para-Máquina (M2M)
- **2.2.1. Client Credentials Grant**
  - 2.2.1.1. **Mecânica:** Usado quando não há um utilizador humano envolvido (ex: o seu Microsserviço de Faturação precisa comunicar com o Microsserviço de Inventário).
  - 2.2.1.2. **Segurança:** O Microsserviço envia o seu próprio `client_id` e `client_secret` diretamente para o *Authorization Server* e recebe um Token.

---

## 3 Domínio: A Anatomia dos Tokens

### 3.1 Access Token vs Refresh Token
- **3.1.1. Access Token (O Bilhete de Entrada)**
  - 3.1.1.1. **Mecânica:** É a credencial usada para aceder à API (Resource Server). Pode ser um *Opaque Token* (uma string aleatória que a API tem de validar batendo no Authorization Server) ou um *JWT* (JSON Web Token, que a API valida localmente através da assinatura criptográfica).
  - 3.1.1.2. **Ciclo de Vida:** Deve ter um tempo de vida muito curto (ex: 5 a 15 minutos) para minimizar os danos caso seja roubado (comprometimento via XSS, por exemplo).
- **3.1.2. Refresh Token (A Chave Mestra)**
  - 3.1.2.1. **Mecânica:** Um token especial de longa duração (dias ou meses) usado **exclusivamente** para pedir novos *Access Tokens* quando os antigos expiram, sem obrigar o utilizador a digitar a password novamente.
  - 3.1.2.2. **Segurança Crítica:** Se roubado, o atacante tem acesso contínuo. Exige mecanismos de *Rotation* (cada vez que é usado, um novo Refresh Token é emitido e o antigo é invalidado) e deve ser guardado no navegador apenas em cookies `HttpOnly`.

---

## 4 Domínio: O Elefante na Sala (OAuth 2.0 vs OIDC)

### 4.1 A Camada de Identidade
- **4.1.1. A Limitação do OAuth 2.0**
  - 4.1.1.1. **O Problema:** O OAuth 2.0 foi desenhado apenas para *Autorização*. O `Access Token` diz "tens acesso", mas não diz "quem tu és". Usar OAuth 2.0 puro para fazer "Login" é uma falha arquitetural (apelidada de *Pseudo-Authentication*).
- **4.1.2. OpenID Connect (OIDC)**
  - 4.1.2.1. **A Solução:** O OIDC é uma camada de identidade construída **em cima** do OAuth 2.0.
  - 4.1.2.2. **ID Token:** Quando usa OIDC (passando o escopo `openid`), além do `Access Token`, recebe um `ID Token` (obrigatoriamente um JWT). Este token contém as informações do perfil do utilizador (nome, email, foto), permitindo que a aplicação Front-end saiba quem está logado.

---

## 5 Domínio: Trade-offs em Arquitetura Distribuída

### 5.1 Vantagens de Adotar OAuth 2.0 (Identity Provider Externo)
- **5.1.1. Desacoplamento de Segurança**
  - 5.1.1.1. Remover a lógica pesada de *Reset de Password*, MFA (Múltiplos Fatores de Autenticação) e bloqueio de força bruta da sua API de negócio, delegando para ferramentas maduras (como Keycloak).
- **5.1.2. Single Sign-On (SSO)**
  - 5.1.2.1. Permite que o utilizador faça login uma única vez e navegue por 10 aplicações diferentes do seu ecossistema corporativo.

### 5.2 Desvantagens e Custos
- **5.2.1. Complexidade de Implementação**
  - 5.2.1.1. O ecossistema é vasto (OAuth 2.0, OIDC, PKCE, JWKS, CORS, Cookies HttpOnly). Um pequeno erro na configuração do fluxo (ex: validar incorretamente a assinatura JWT ou vazar o client secret) expõe toda a infraestrutura.
- **5.2.2. Overhead de Rede e Latência**
  - 5.2.2.1. Se usar *Opaque Tokens*, a sua API (Resource Server) terá de fazer uma chamada HTTP extra para o *Authorization Server* (Endpoint de Introspecção) em CADA requisição para validar se o token ainda é válido, dobrando a latência do sistema. (Daí a preferência moderna por usar JWTs como Access Tokens para validação local assíncrona).