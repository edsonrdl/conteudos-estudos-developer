# 📚 Guia de Estudos: Postman (API Platform)
#api #ferramentas #rest #automacao #testes

> [!info] Visão Geral
> O Postman começou como um simples cliente HTTP REST, mas evoluiu para uma plataforma corporativa de gerenciamento do ciclo de vida de APIs. Em termos arquiteturais, o seu verdadeiro poder não está apenas em disparar requisições, mas no seu motor de execução Node.js embutido (Sandbox), que permite criar testes unitários/E2E agressivos (`pm.test`), mockar servidores falsos para o front-end e integrar coleções inteiras em pipelines de CI/CD usando o Newman.

---

## 1 Domínio: Hierarquia Lógica e Escopo

### 1.1 Workspaces e Collections
- **1.1.1. O Isolamento de Domínios**
  - 1.1.1.1. **Workspaces:** É a fronteira máxima de isolamento (físico e de permissões). Usado para separar equipas inteiras ou projetos corporativos distintos. Todo o histórico, variáveis globais e mocks vivem dentro do Workspace.
  - 1.1.1.2. **Collections:** Agrupamentos lógicos de requisições, geralmente espelhando os *Controllers* ou domínios da sua API (ex: `Users API`, `Billing API`).
- **1.1.2. Herança em Cascata (O Padrão Ouro)**
  - 1.1.2.1. Nunca se configura Autorização ou Scripts *Request por Request*. A arquitetura correta exige que o Token (Aba Auth) e os Scripts de Setup (Aba Pre-request Script) sejam configurados na raiz da **Collection** ou da **Folder** (Pasta).
  - 1.1.2.2. Todos os requests internos devem ter o Auth configurado como `Inherit auth from parent`. Isso garante que uma mudança no modelo de segurança da API exija atualização num único lugar.

---

## 2 Domínio: O Motor de Variáveis e Ambientes

### 2.1 Contexto Dinâmico e Precedência
- **2.1.1. A Pirâmide de Escopo**
  - 2.1.1.1. O Postman resolve variáveis com nomes duplicados usando uma ordem estrita de precedência (do mais amplo para o mais restrito). Se houver conflito, a variável mais "local" vence.
  - 2.1.1.2. **A Ordem:** 1. Global -> 2. Collection -> 3. Environment -> 4. Data (Arquivos CSV/JSON) -> 5. Local (criadas via script de execução).
- **2.1.2. Environments (Ambientes)**
  - 2.1.2.1. O local ideal para guardar credenciais mutáveis (`{{base_url}}`, `{{client_secret}}`). Permite trocar a bateria de testes de "Homologação" para "Produção" com um único clique.
- **2.1.3. Variáveis Dinâmicas Nativas**
  - 2.1.3.1. O Postman possui geradores *faker* embutidos para testes de carga e fuzzing. Ao invés de escrever "teste@teste.com" no JSON, você injeta `{{$randomEmail}}`, `{{$guid}}` ou `{{$timestamp}}`. A cada disparo, um valor novo é gerado em tempo real.

---

## 3 Domínio: Scripting e Automação (Sandbox Node.js)

### 3.1 Pre-request Scripts (Antes do Voo)
- **3.1.1. Manipulação e Criptografia Prévia**
  - 3.1.1.1. **Mecânica:** Código JavaScript executado *antes* da requisição HTTP sair da sua máquina.
  - 3.1.1.2. **Aplicações Reais:** Usado para gerar hashes criptográficos exigidos por APIs bancárias (ex: HMACS-SHA256). O script lê o payload original (`pm.request.body`), gera a assinatura via biblioteca `crypto-js` embutida, e injeta o hash gerado diretamente nos Headers antes de disparar.

### 3.2 Tests (Pós-Voo e Asserções)
- **3.1.1. Validação Automática de Contratos**
  - 3.1.1.1. **Mecânica:** Código executado assim que a resposta (`pm.response`) volta do servidor. Usa a sintaxe BDD (Behavior-Driven Development) da biblioteca Chai.
  - 3.1.1.2. **Testes de Contrato (Schema Validation):** Muito superior a apenas validar se `status === 200`. Você pode usar o Ajv (JSON Schema Validator) dentro do Postman para verificar se a estrutura do JSON recebido não foi quebrada na última atualização do back-end.
  - 3.1.1.3. **Exemplo Conceitual de Teste:** Extrair o `Access Token` da resposta de Login usando `pm.response.json().token` e gravá-lo automaticamente no Environment usando `pm.environment.set("jwt", token)`.

---

## 4 Domínio: Integração Contínua e Ferramentas Cloud

### 4.1 CLI e Pipelines (Postman Newman)
- **4.1.1. Tirando o Postman da UI**
  - 4.1.1.1. **O Problema:** A interface visual é ótima para desenvolvimento, mas inútil para processos de CI/CD (GitHub Actions, GitLab CI).
  - 4.1.1.2. **A Solução (Newman):** É a interface de linha de comando oficial do Postman. Você exporta a sua Collection e o seu Environment, e o Newman roda milhares de requisições de teste em background num servidor Linux "headless" durante a compilação do seu projeto. Se um `pm.test` falhar, o Newman retorna *Exit Code 1* e quebra o pipeline de deploy, impedindo o bug de ir para produção.

### 4.2 Ferramentas Arquiteturais (Mocks e Monitors)
- **4.2.1. Mock Servers (Contrato First)**
  - 4.2.1.1. O Front-end não deve esperar o Back-end terminar de programar para começar a trabalhar. No Postman, você pode criar uma resposta "Mock" (um JSON fictício salvo). O Postman gera uma URL pública na nuvem (`https://<id>.mock.pstmn.io`) que devolve esse JSON instantaneamente, permitindo que as equipas trabalhem em paralelo.
- **4.2.2. Monitors (Cron Jobs de API)**
  - 4.2.2.1. Permite agendar a execução da sua Collection na nuvem do Postman (ex: a cada 15 minutos de vários datacenters diferentes). Se a sua API de Produção cair ou responder acima de 500ms, o Monitor chumba o teste e aciona um *Webhook* no seu Slack/PagerDuty.

---

## 5 Domínio: Trade-offs de Produção (Postman vs Insomnia)

### 5.1 A Mudança Arquitetural Recente
- **5.1.1. Cloud-Forced vs Local-First**
  - 5.1.1.1. **O Peso do Postman:** O Postman recentemente descontinuou o uso completo offline em favor da sincronização forçada em nuvem (exigindo login na plataforma). Isso gerou problemas de *compliance* de segurança em grandes bancos, pois *tokens* e metadados sensíveis são empurrados para os servidores americanos da empresa.
  - 5.1.1.2. **Decisão:** Se a necessidade é apenas "disparar requisições e debugar gRPC localmente", o Insomnia vence pela leveza e privacidade. Se a necessidade é gerir uma suíte de testes de QA e mocks partilhados com dezenas de equipas corporativas, o ecossistema do Postman é indiscutível.