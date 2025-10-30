O **Role (Função IAM)** é uma identidade dentro da AWS que possui permissões, mas que **não está associada a uma pessoa ou chave de acesso específica de longa duração**.

### Como Funciona:

1. **Não tem Credenciais Permanentes:** Diferente de um Usuário IAM, um Role não tem suas próprias senhas ou chaves de acesso permanentes.
    
2. **Assumir o Role (`AssumeRole`):** O Role deve ser **assumido** por uma entidade confiável (como um serviço AWS, um Usuário IAM ou uma aplicação externa). Ao assumir o Role, a entidade recebe **credenciais temporárias** (chaves de acesso de curta duração) para realizar as ações permitidas.
    
3. **Para que Serve a Permissão (Trust Policy - Política de Confiança):**
    
    - **É o _primeiro_ passo.**
        
    - Você define **quem** (o "Principal") pode assumir o Role. É como a porta de entrada.
        
    - **Exemplo:** Você permite que um serviço AWS (como o **EC2** - servidor virtual) ou um Usuário IAM em outra conta **assuma** esse Role.
        

### Para que Você Dá a Permissão (O Role):

Você concede um Role a:

- **Serviços AWS:** Exemplo: Um servidor **EC2** precisa ler dados de um bucket **S3**. Você cria um Role com a permissão de leitura do S3 e anexa ele ao EC2. O EC2 assume esse Role.
    
- **Usuários em outra Conta AWS:** Para permitir acesso seguro entre contas (cross-account).
    
- **Aplicações Externas:** Para autenticação externa (federação de identidade).
    

---

## 📝 Exemplo Prático de uma Política de Permissões IAM (O JSON)

As permissões reais do Role são definidas em um documento **JSON (Política de Permissões)**.

Imagine que você quer que um servidor **EC2** possa **ler** (mas não apagar ou escrever) um arquivo específico dentro de um bucket **S3** chamado `meu-bucket-de-dados`.

JSON

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::meu-bucket-de-dados/dados-sensivel.txt"
        }
    ]
}
```

### O que Cada Coisa no JSON Significa:

|**Elemento JSON**|**Descrição**|**Exemplo no Contexto**|
|---|---|---|
|**`Version`**|Versão do formato da linguagem da política IAM.|Sempre `"2012-10-17"`.|
|**`Statement`**|É um bloco que contém uma ou mais regras de permissão.|Contém a regra de acesso.|
|**`Effect`**|Define se a regra é para **Permitir** (`Allow`) ou **Negar** (`Deny`) o acesso.|`"Allow"`: Permite a ação. Uma negação sempre se sobrepõe a uma permissão.|
|**`Action`**|**O que** a entidade pode fazer. É a API ou operação do serviço AWS.|`"s3:GetObject"`: Permite a ação de **ler** (fazer o download) de um objeto no S3. Outros exemplos seriam `s3:PutObject` (escrever), `ec2:RunInstances` (criar uma VM), etc.|
|**`Resource`**|**Onde** a ação pode ser executada. Este é o ponto que você não entendeu! É o Recurso específico da AWS.|**O Recurso é o alvo da ação.** `"arn:aws:s3:::meu-bucket-de-dados/dados-sensivel.txt"`: A ação de leitura **só** pode ser executada neste arquivo específico.|

### 🎯 Detalhando o `Resource` (Recurso)

O **`Resource`** é o **ARN (Amazon Resource Name)** do objeto ou serviço ao qual você está concedendo (ou negando) a permissão. Ele é crucial para o princípio do **Mínimo Privilégio**, garantindo que a entidade só tenha acesso ao estritamente necessário.

**Estrutura de um ARN:** `arn:partition:service:region:account-id:resource`

|**Parte do ARN**|**Significado**|**Exemplo no Contexto**|
|---|---|---|
|`arn:aws`|Indica que é um ARN da AWS.||
|`s3`|O serviço AWS (S3).||
|`:::`|Região e ID da Conta (não aplicáveis para S3 global de bucket/objeto).||
|`meu-bucket-de-dados`|O nome do Bucket S3.||
|`/dados-sensivel.txt`|O caminho para o objeto **específico** dentro do bucket.||

#### **Recurso (Resource) em Diferentes Cenários:**

|**Objetivo**|**Resource JSON**|**Explicação**|
|---|---|---|
|**Acesso a um Arquivo Específico:**|`"arn:aws:s3:::meu-bucket/arquivo.log"`|Permite acesso **apenas** a `arquivo.log`.|
|**Acesso a Todos os Arquivos de um Bucket:**|`"arn:aws:s3:::meu-bucket/*"`|O `*` (asterisco) é um **wildcard** que representa _qualquer coisa_. Permite acesso a todos os objetos.|
|**Acesso a um Bucket Inteiro:**|`"arn:aws:s3:::meu-bucket"`|Permite apenas ações que se aplicam ao bucket em si (como listar o bucket, mas não ler objetos).|
|**Acesso a Todas as Instâncias EC2:**|`"arn:aws:ec2:us-east-1:123456789012:instance/*"`|Permite ações em **qualquer** instância EC2 na região `us-east-1` daquela conta.|
|**Acesso a TUDO (Não Recomendado!):**|`"*"`|Permite a ação em **qualquer** recurso na AWS. Use com extrema cautela, apenas para ações que são globalmente permitidas (como listar regiões).|

**Em resumo:**

1. Você cria o **Role**.
    
2. Você define a **Política de Confiança** (quem pode assumir o Role).
    
3. Você define a **Política de Permissões (JSON)** e a anexa ao Role (o que a entidade pode fazer).
    
4. O `Resource` na Política de Permissões define o **alvo exato** da ação (`Action`).
    

