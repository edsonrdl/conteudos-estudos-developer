## 💻 Comandos Git: Uso Contextualizado e Real

Os comandos Git se enquadram em três grandes fases do desenvolvimento: **Preparação**, **Versionamento** e **Sincronização Remota/Colaboração**.

### 1. 🌐 Comandos de Preparação e Estrutura

São usados no início do projeto para configurar o ambiente e obter o código.

| **Comando**       | **Contexto de Uso Real**                                                                                                                                               | **Necessidade**                                                                                             | **Exemplo**                                                   |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| `git init`        | Você acabou de criar uma pasta vazia (`mkdir meu-projeto`) e quer transformá-la em um repositório Git para começar a rastrear o histórico.                             | **Iniciar o versionamento** de um projeto local do zero.                                                    | `cd meu-projeto` $\rightarrow$ `git init`                     |
| `git clone <url>` | Você entra em uma nova equipe ou deseja contribuir para um projeto _Open Source_ existente no GitHub.                                                                  | **Obter uma cópia de trabalho completa** (incluindo todo o histórico) de um repositório remoto.             | `git clone https://github.com/usuario/projeto.git`            |
| `git config`      | Você configura o seu nome e e-mail **pela primeira vez** no seu computador. Essa informação será a **sua assinatura digital** em cada _commit_.                        | Garantir que o Git saiba **quem está fazendo as alterações** para fins de auditoria e rastreabilidade.      | `git config --global user.name "Seu Nome"`                    |
| `.gitignore`      | Você está trabalhando em um projeto web e o Node.js cria a pasta temporária `node_modules/` que deve ser ignorada, pois é muito grande e irrelevante para o histórico. | **Excluir arquivos/pastas** que não devem ser versionados (ex: arquivos de _build_, senhas locais, caches). | (Cria-se um arquivo `.gitignore` com a linha `node_modules/`) |

---

### 2. 📸 Comandos de Versionamento Local (Working Flow)

Estes são os comandos que você usará repetidamente para registrar suas alterações no seu repositório local.

| **Comando**                        | **Contexto de Uso Real**                                                                                                                | **Necessidade**                                                                                                                                           | **Exemplo**                                                       |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `git status`                       | Você precisa saber: "O que eu mudei desde o último _commit_?" ou "Os meus arquivos já estão prontos para serem salvos?".                | **Visualizar o status atual** do seu diretório de trabalho, identificando arquivos novos, modificados ou preparados (na _staging area_).                  | `git status` (Você o usa antes de todo `git add` ou `git commit`) |
| `git add <arquivo>` ou `git add .` | Você terminou de implementar a nova lógica no arquivo `login.js` e quer prepará-lo para ser salvo em um _commit_.                       | **Mover as alterações** do seu diretório de trabalho para a **Staging Area** (área de preparação), indicando o que deve ser incluído no próximo _commit_. | `git add login.js` ou, para tudo, `git add .`                     |
| `git commit -m "msg"`              | O novo recurso de login está pronto e a alteração está na _Staging Area_. Você quer registrar essa versão permanentemente no histórico. | **Gravar as alterações preparadas** como um novo ponto no histórico do projeto (_snapshot_). A **mensagem** é crucial para entender o que foi feito.      | `git commit -m "Implementa validação de formulário de login"`     |
| `git log`                          | Você precisa encontrar a _hash_ (ID) do _commit_ de uma semana atrás para ver exatamente como era o código.                             | **Visualizar o histórico** completo de _commits_ (quem fez, quando e o que foi feito) no seu _branch_ atual.                                              | `git log --oneline` (versão resumida)                             |
| `git diff`                         | Você alterou um arquivo e quer rever exatamente **quais linhas foram adicionadas ou removidas** antes de fazer o `git add`.             | **Comparar as diferenças** entre o seu diretório de trabalho e o último _commit_ (ou a _Staging Area_).                                                   | `git diff index.html`                                             |

---

### 3. 🌳 Comandos de Ramificação (Branching)

Usados para isolar o desenvolvimento de recursos e organizar o trabalho em equipe.

| **Comando**              | **Contexto de Uso Real**                                                                                                            | **Necessidade**                                                                                     | **Exemplo**                                                     |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| `git branch <nome>`      | Você está na _branch_ principal (`main`) e vai começar a trabalhar em um novo sistema de pagamento.                                 | **Criar uma nova _branch_** para isolar o desenvolvimento de uma funcionalidade ou correção de bug. | `git branch feature/pagamento`                                  |
| `git checkout <nome>`    | Você precisa pausar o trabalho na _branch_ de pagamento e voltar para a _branch_ principal para corrigir um bug urgente.            | **Alternar** entre as _branches_ existentes.                                                        | `git checkout main`                                             |
| `git checkout -b <nome>` | (Comando de atalho) Você cria a _branch_ e já quer começar a trabalhar nela imediatamente.                                          | **Criar uma nova _branch_ e alternar** para ela em um único comando.                                | `git checkout -b bugfix/erro-tela-inicial`                      |
| `git merge <nome>`       | A _branch_ `feature/pagamento` está completa, testada e aprovada. Você precisa integrar as mudanças na _branch_ principal (`main`). | **Unir (fundir)** o histórico de uma _branch_ em outra.                                             | `git checkout main` $\rightarrow$ `git merge feature/pagamento` |

---

### 4. 📤 Comandos de Sincronização Remota (Colaboração)

Usados para interagir com o repositório hospedado em plataformas como GitHub, GitLab ou Bitbucket.

| **Comando**                   | **Contexto de Uso Real**                                                                                                                                | **Necessidade**                                                                                        | **Exemplo**                                     |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | ----------------------------------------------- |
| `git remote add origin <url>` | Você acabou de fazer o primeiro `git init` localmente e precisa vincular esse repositório à sua conta no GitHub.                                        | **Definir um apelido** (geralmente `origin`) para o URL do seu repositório remoto.                     | `git remote add origin https://.../projeto.git` |
| `git push origin <branch>`    | Você terminou vários _commits_ e quer enviar (fazer o upload) de todo esse histórico para que o restante da equipe possa ver e o GitHub tenha o backup. | **Enviar os _commits_ locais** para o repositório remoto.                                              | `git push origin main`                          |
| `git pull origin <branch>`    | Você chega para trabalhar e sabe que um colega de equipe enviou novas alterações para o GitHub. Você precisa das últimas novidades.                     | **Baixar e automaticamente mesclar** as alterações do repositório remoto para o seu repositório local. | `git pull origin main`                          |
| `git fetch origin`            | Você quer ver quais _branches_ e _commits_ novos estão no repositório remoto **sem aplicar ou mesclar nada** no seu código local.                       | **Baixar as informações** do histórico remoto para o seu Git local, mas sem modificar seu código.      | `git fetch origin`                              |

---

### 5. ⚠️ Comandos de Desfazer e Limpeza (Avançados)

Esses comandos são cruciais quando você comete erros ou precisa reescrever o histórico.

|**Comando**|**Contexto de Uso Real**|**Necessidade**|**Exemplo**|
|---|---|---|---|
|`git checkout -- <arquivo>`|Você fez alterações em um arquivo, mas não fez `git add` e percebeu que elas estragaram o código. Você quer **descartar** essas alterações e restaurar o arquivo para a última versão commitada.|Descartar mudanças **não-comitadas** no seu diretório de trabalho.|`git checkout -- style.css`|
|`git reset <arquivo>`|Você adicionou acidentalmente um arquivo de senha à _Staging Area_ com `git add .` e precisa removê-lo de lá antes de comitar.|Remover um arquivo da _Staging Area_ (desfazer o `git add`), mantendo as alterações no seu código.|`git reset secrets.txt`|
|`git reset --hard HEAD~1`|Você acabou de fazer um _commit_ (o último) com a mensagem e o código errados e ninguém mais o viu. Você quer **apagar esse _commit_** completamente.|**Apagar permanentemente** o último _commit_ do seu histórico local (use com extrema cautela!).|`git reset --hard HEAD~1`|
|`git revert <hash>`|Você enviou um _commit_ para o GitHub que introduziu um bug sério no código de produção. Você não pode apagá-lo (pois já foi compartilhado), mas precisa desfazer suas ações.|**Cria um novo _commit_** que desfaz as alterações de um _commit_ anterior, mantendo o histórico intacto e seguro.|`git revert 4b68a7d`|

Qual desses contextos do mundo real você gostaria de explorar com mais profundidade? Posso fornecer um fluxo de trabalho detalhado para um cenário específico (ex: "Corrigindo um bug urgente em produção usando _branching_").