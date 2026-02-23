### 1. Os Atores do Processo

Antes de rodar qualquer comando, você precisa entender quem é quem na Criptografia Assimétrica (Infraestrutura de Chaves Públicas - PKI):

- **Você (A Aplicação/Servidor):** Quem quer provar que é confiável.
    
- **O Cliente (Navegador ou a Salesforce):** Quem está acessando e quer ter certeza de que você é quem diz ser.
    
- **A Autoridade Certificadora (CA):** O "Cartório". Uma entidade globalmente confiável (como DigiCert, Let's Encrypt, GlobalSign) que atesta que você é você.
    

---

### 2. O Processo de Criação (O que o OpenSSL gera)

Quando você vai criar um certificado SSL, o processo ocorre em **três etapas obrigatórias**. O OpenSSL vai gerar arquivos diferentes em cada etapa.

#### Etapa 1: A Geração da Chave Privada (Private Key)

Tudo começa aqui. O OpenSSL usa pura matemática para gerar um número gigantesco e aleatório.

- **O Arquivo:** Geralmente salvo como `.key` (ex: `minha-chave.key`).
    
- **O que é:** É o seu segredo absoluto. É a base matemática de toda a sua segurança. Ela **NUNCA** deve sair do seu servidor (ou do AWS Secrets Manager).
    
- **Para que serve:** Ela serve para **descriptografar** mensagens enviadas para você e para **assinar digitalmente** envios (como provar sua identidade no mTLS ou assinar um JWT RS256).
    

#### Etapa 2: A Geração do CSR (Certificate Signing Request)

A chave privada sozinha não diz quem você é (não tem seu nome, nem sua empresa). Você precisa criar um "Requerimento de Certificado".

- **O Arquivo:** Salvo como `.csr` (ex: `pedido.csr`).
    
- **O que é:** O OpenSSL pega a sua Chave Pública (que é o par matemático da sua Chave Privada), junta com os dados da sua empresa (Nome, Domínio, País) e empacota isso.
    
- **Para que serve:** É o formulário que você envia para a Autoridade Certificadora (CA). Você está dizendo: _"CA, eu sou dono desta chave pública e deste domínio. Por favor, valide."_
    

#### Etapa 3: A Emissão do Certificado pela CA

Você envia o `.csr` para a Autoridade Certificadora (CA). A CA verifica se você realmente é dono daquele domínio e empresa. Se estiver tudo certo, a CA pega o seu arquivo, carimba digitalmente com a chave privada _dela_ e te devolve o certificado final.

- **O Arquivo:** Salvo geralmente como `.crt`, `.cer` ou `.pem` (O Certificado Público).
    
- **O que é:** É o seu "Alvará de Funcionamento" digital. Ele contém a sua Chave Pública e a assinatura da CA dizendo que você é confiável.
    
- **Para que serve:** É o arquivo que você distribui para o mundo. Qualquer um pode baixar. É ele que prova a sua identidade.
    

---

### 3. Mas afinal, o que é o formato `.PEM`?

É aqui que 90% dos desenvolvedores se confundem. **PEM não é um tipo de chave, é apenas um formato de texto.**

PEM (_Privacy Enhanced Mail_) é simplesmente uma forma de converter dados binários brutos em texto puro (Base64) para que você possa abrir no Bloco de Notas, copiar e colar.

- Um arquivo `.pem` pode conter **apenas a Chave Privada**.
    
- Um arquivo `.pem` pode conter **apenas o Certificado Público**.
    
- Um arquivo `.pem` pode conter **OS DOIS juntos** (um abaixo do outro).
    

Você vai identificar o que tem dentro do `.pem` lendo a primeira linha do arquivo:

- Se começar com `-----BEGIN PRIVATE KEY-----` -> É o seu segredo. Vai para o Secrets Manager.
    
- Se começar com `-----BEGIN CERTIFICATE-----` -> É o seu certificado público carimbado pela CA.
    

---

### 4. Como a instância prova que é confiável? (A Mágica do HTTPS)

Imagine que você subiu sua API na AWS e configurou o certificado. Um cliente bate no seu domínio `https://api.suaempresa.com.br`. Como o cliente confia?

1. **Apresentação:** Seu servidor entrega o **Certificado Público (`.crt` / `.pem`)** para o cliente.
    
2. **Validação da Cadeia (Trust):** O navegador/sistema do cliente olha para o certificado e vê: _"Hum, este certificado foi assinado pela Let's Encrypt. Eu (navegador) confio na Let's Encrypt de fábrica. Logo, confio neste certificado."_
    
3. **O Desafio Matemático:** O cliente diz: _"Ok, o certificado é válido. Mas como eu sei que você não roubou esse arquivo de outro servidor? Prove que você é o dono."_ O cliente criptografa uma mensagem aleatória usando a **Chave Pública** que estava no certificado e envia para o servidor.
    
4. **A Prova:** O seu servidor recebe a mensagem embaralhada e usa a **Chave Privada (`.key`)** para descriptografar. Ele devolve a resposta clara. O cliente pensa: _"Perfeito, só o verdadeiro dono da Chave Privada conseguiria ler isso. O túnel HTTPS está formado."_
    

---

### 5. Resumo das Responsabilidades: O que você usa onde?

Separando responsabilidades na sua arquitetura, especialmente em integrações de alto nível como a da Salesforce:

|**Artefato**|**O que é de verdade?**|**Onde eu uso na Arquitetura?**|**O que ele garante?**|
|---|---|---|---|
|**Chave Privada** (`.key` ou `.pem`)|O motor matemático secreto.|Fica escondido na sua aplicação (ex: AWS Secrets Manager). O Spring Boot usa na memória.|**1.** Assina o token JWT.<br><br>  <br><br>**2.** Realiza a conexão do túnel mTLS provando que o servidor é seu.|
|**Certificado Público** (`.crt` ou `.pem`)|O "crachá" assinado pela CA contendo sua chave pública.|**1.** É enviado ao cliente no handshake HTTPs.<br><br>  <br><br>**2.** É feito o upload no portal da Salesforce.|Diz ao mundo quem você é e permite que outros validem as assinaturas feitas pela sua Chave Privada.|
|**Autoridade Certificadora (CA)**|A Entidade de Confiança (O "Cartório").|Fica fora do seu sistema. Os sistemas operacionais (Windows, Linux) já vêm com uma lista de CAs confiáveis instaladas.|Garante a terceiros que o seu Certificado Público não é falso.|

**O Trade-off de usar uma CA vs. Autoassinado (Self-Signed):**

- **Vantagem do CA:** Qualquer navegador ou API do mundo vai confiar em você automaticamente. Custa dinheiro (em alguns casos) e dá trabalho para renovar anualmente.
    
- **Vantagem do Autoassinado (O que o OpenSSL faz puro):** Você mesmo gera e assina seu certificado. É grátis e rápido. **Desvantagem:** Navegadores vão dar tela vermelha de "Site Não Seguro" e APIs (como Salesforce) rejeitarão a conexão de imediato, a menos que você envie o arquivo do certificado antes e peça para eles confiarem na sua assinatura manualmente.

---


### 1. O que fica com a ASI (Sua Instância / AWS)

Na sua aplicação (o lado que **cria** e **envia** o JWT), você precisa do motor de assinatura.

- **O Arquivo:** A **Chave Privada** (`.key` ou o arquivo `.pem` que começa com `-----BEGIN PRIVATE KEY-----`).
    
- **Nível de Sensibilidade:** **CRÍTICO / MÁXIMO**. Se esse arquivo vazar, qualquer pessoa no mundo poderá gerar tokens falsos se passando pela sua empresa. Ele deve viver apenas no AWS Secrets Manager e na memória da sua aplicação Java durante a execução.
    
- **Como é usado:** O seu código Spring Boot usa esta Chave Privada para "carimbar" (assinar matematicamente) o JWT antes de enviá-lo.
    

### 2. O que você entrega para a Salesforce (A Instância de Destino)

Você não envia nenhum "segredo" ou "senha" para a Salesforce. O conceito de "segredo compartilhado" não existe aqui.

- **O Arquivo:** O **Certificado Público** (`.crt` ou o arquivo `.pem` que começa com `-----BEGIN CERTIFICATE-----`).
    
- **Nível de Sensibilidade:** **NENHUM**. Esse arquivo é literalmente público. Você poderia postá-lo no Twitter e não haveria risco de segurança.
    
- **Como é entregue:** Normalmente, você não envia isso em cada requisição. Você entra no painel de administração da Salesforce (no setup do _Connected App_), clica em "Upload de Certificado" e envia esse arquivo `.crt` uma única vez.
    
- **Para que a Salesforce usa:** Quando o seu JWT chega lá, a Salesforce usa este certificado público para validar se a assinatura do JWT bate com o seu payload. Se a matemática fechar, eles sabem que só o verdadeiro dono da Chave Privada (a ASI) poderia ter gerado aquele token.
    

---

### 3. O que vai DENTRO do JWT (E o que é sensível lá dentro?)

Isso é fundamental: **Por padrão, o JWT (JWS - JSON Web Signature) NÃO É CRIPTOGRAFADO.** Ele é apenas codificado em Base64 e assinado. Qualquer pessoa que interceptar seu token pode ir no site `jwt.io`, colar o token e ler tudo o que está no _Payload_. A assinatura digital apenas impede que alguém _altere_ os dados, mas não impede a leitura.

Portanto, a regra de ouro sobre o que colocar no _Payload_ (os _Claims_) do seu JWT:

|**Categoria**|**O que colocar no Payload do JWT**|**Motivo Técnico / Trade-off**|
|---|---|---|
|**Obrigatórios (Padrão OAuth)**|`iss` (Issuer - O Client ID da ASI), `aud` (Audience - A URL da Salesforce), `exp` (Data de expiração curta, ex: 3 minutos), `sub` (O e-mail do usuário de integração).|São os identificadores que a Salesforce exige para saber quem está chamando e se o token ainda é válido no tempo.|
|**Permitidos (Contexto)**|Escopos de permissão (ex: `scope: "api web"`), IDs de transação.|Ajudam no controle de acesso (Autorização) sem expor dados de negócio.|
|**ESTRITAMENTE PROIBIDOS**|**Senhas**, **Chaves Privadas**, CPFs de vestibulandos, dados de cartão de crédito.|Como o JWT é apenas codificado em Base64, qualquer um que interceptar a requisição (caso não houvesse o mTLS) leria os dados sensíveis em texto claro.|

---

### 4. Trade-off: Por que a arquitetura Assimétrica (RS256) é superior?

Se você usasse o JWT comum (Simétrico / HS256), você e a Salesforce teriam que combinar uma senha (ex: `ASI-senha-123`). O seu Java usaria essa senha para assinar, e a Salesforce usaria a _mesma_ senha para verificar.

**O Problema (Vetor de Ataque):** Se o banco de dados da Salesforce for hackeado e vazarem essa senha, o hacker agora pode gerar tokens falsos no seu nome.

**A Vantagem do Assimétrico (O que você está fazendo):** Com o OpenSSL e as chaves RSA, você entregou apenas o **Certificado Público** para a Salesforce. Se a Salesforce for hackeada inteira amanhã e vazarem o seu certificado, o hacker **não consegue** gerar tokens falsos no nome da ASI. Por quê? Porque para _gerar_ um token válido é matematicamente obrigatório ter a **Chave Privada**, e essa chave nunca saiu da sua AWS.

### Resumo do Fluxo do JWT

1. **Setup:** Você gera as chaves e faz upload do Certificado Público na Salesforce.
    
2. **Execução:** Seu Java monta um JSON (sem senhas, só com IDs), assina esse JSON usando a Chave Privada escondida na AWS e gera a string JWT.
    
3. **Envio:** A string JWT vai no `Authorization: Bearer <token>` do cabeçalho HTTP.
    
4. **Validação:** A Salesforce recebe o JWT, usa o Certificado Público que você deu a eles lá no Setup e diz: _"Assinatura validada. É a ASI. Podem cadastrar o vestibulando"_.