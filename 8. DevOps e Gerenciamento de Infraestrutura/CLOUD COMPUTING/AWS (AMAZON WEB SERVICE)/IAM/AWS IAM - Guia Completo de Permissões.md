# AWS IAM - Guia Completo de Permissões

Vou te explicar de forma completa como funciona o sistema de permissões no AWS IAM.

## 🎯 Conceitos Fundamentais

### 1. **Identidades IAM**

**Usuários IAM (IAM Users)**

- Pessoas ou aplicações que precisam acessar a AWS
- Credenciais permanentes (senha + access keys)
- Cada usuário tem ARN único: `arn:aws:iam::123456789012:user/nome`

**Grupos IAM (IAM Groups)**

- Coleção de usuários
- Não podem ser usados em políticas de recursos
- Apenas para gerenciar permissões de múltiplos usuários

**Roles IAM (IAM Roles)**

- Identidades assumíveis temporariamente
- Não têm credenciais permanentes
- Usadas por: serviços AWS, aplicações, usuários federados
- ARN: `arn:aws:iam::123456789012:role/nome`

**Identidades Federadas**

- SSO corporativo (SAML 2.0, OpenID Connect)
- AWS IAM Identity Center (antigo AWS SSO)
- Cognito User Pools

---

## 📋 Tipos de Políticas (Policies)

### **1. Identity-Based Policies (Políticas Baseadas em Identidade)**

Anexadas diretamente a identidades (usuários, grupos, roles).

**a) Managed Policies (Gerenciadas)**

json

```json
// AWS Managed Policy (criada pela AWS)
// Exemplo: arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess

// Customer Managed Policy (criada por você)
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::meu-bucket",
        "arn:aws:s3:::meu-bucket/*"
      ]
    }
  ]
}
```

**b) Inline Policies (Embutidas)**

- Anexadas diretamente e exclusivas a uma identidade
- São deletadas quando a identidade é deletada

json

```json
// Inline policy diretamente no usuário
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "dynamodb:*",
      "Resource": "arn:aws:dynamodb:us-east-1:123456789012:table/Producao"
    }
  ]
}
```

### **2. Resource-Based Policies (Políticas Baseadas em Recurso)**

Anexadas diretamente aos recursos (S3, SQS, SNS, Lambda, etc).

json

```json
// Bucket Policy (S3)
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/MeuRole"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::meu-bucket/*"
    }
  ]
}
```

### **3. Permission Boundaries (Limites de Permissão)**

Define o máximo de permissões que uma identidade pode ter.

json

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*",
        "ec2:*",
        "dynamodb:*"
      ],
      "Resource": "*"
    }
  ]
}
```

### **4. Service Control Policies (SCPs)**

Usadas no AWS Organizations para limitar permissões em contas.

json

```json
// SCP que impede exclusão de recursos
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "*:Delete*",
        "*:Terminate*"
      ],
      "Resource": "*"
    }
  ]
}
```

### **5. Session Policies**

Aplicadas quando você assume um role (via AssumeRole, STS).

json

```json
// Limita permissões durante a sessão
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::bucket-especifico/*"
    }
  ]
}
```

---

## 🔧 Como Atribuir Permissões

### **Método 1: Anexar Policies a Usuários**

bash

```bash
# Via AWS CLI
aws iam attach-user-policy \
  --user-name joao \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess

# Criar e anexar custom policy
aws iam create-policy \
  --policy-name MinhaPolicy \
  --policy-document file://policy.json

aws iam attach-user-policy \
  --user-name joao \
  --policy-arn arn:aws:iam::123456789012:policy/MinhaPolicy
```

### **Método 2: Usar Grupos**

bash

```bash
# Criar grupo
aws iam create-group --group-name Desenvolvedores

# Anexar policy ao grupo
aws iam attach-group-policy \
  --group-name Desenvolvedores \
  --policy-arn arn:aws:iam::aws:policy/PowerUserAccess

# Adicionar usuário ao grupo
aws iam add-user-to-group \
  --group-name Desenvolvedores \
  --user-name joao
```

### **Método 3: Criar e Usar Roles**

**Para Serviços AWS:**

bash

```bash
# Criar role para EC2
aws iam create-role \
  --role-name EC2-S3-Access \
  --assume-role-policy-document '{
    "Version": "2012-10-17",
    "Statement": [{
      "Effect": "Allow",
      "Principal": {"Service": "ec2.amazonaws.com"},
      "Action": "sts:AssumeRole"
    }]
  }'

# Anexar permissões
aws iam attach-role-policy \
  --role-name EC2-S3-Access \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess

# Criar instance profile e anexar role
aws iam create-instance-profile --instance-profile-name EC2-S3-Profile
aws iam add-role-to-instance-profile \
  --instance-profile-name EC2-S3-Profile \
  --role-name EC2-S3-Access
```

**Para Cross-Account Access:**

json

```json
// Trust policy permitindo outra conta assumir o role
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::999999999999:root"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "senha-secreta-123"
        }
      }
    }
  ]
}
```

### **Método 4: Policies Baseadas em Recurso**

bash

```bash
# Bucket Policy para S3
aws s3api put-bucket-policy \
  --bucket meu-bucket \
  --policy file://bucket-policy.json

# Lambda Resource Policy
aws lambda add-permission \
  --function-name minhaFuncao \
  --statement-id AllowS3Invoke \
  --action lambda:InvokeFunction \
  --principal s3.amazonaws.com \
  --source-arn arn:aws:s3:::meu-bucket
```

### **Método 5: Permission Boundaries**

bash

```bash
aws iam put-user-permission-boundary \
  --user-name desenvolvedor \
  --permissions-boundary arn:aws:iam::123456789012:policy/LimiteBoundary
```

---

## 🔐 Estrutura de uma Policy

json

```json
{
  "Version": "2012-10-17",  // Versão da linguagem de policy
  "Statement": [
    {
      "Sid": "PermitirLeituraS3",  // Identificador opcional
      "Effect": "Allow",  // Allow ou Deny
      "Principal": {  // Quem (apenas em resource-based policies)
        "AWS": "arn:aws:iam::123456789012:user/joao",
        "Service": "ec2.amazonaws.com"
      },
      "Action": [  // Quais ações
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [  // Em quais recursos
        "arn:aws:s3:::meu-bucket",
        "arn:aws:s3:::meu-bucket/*"
      ],
      "Condition": {  // Condições opcionais
        "IpAddress": {
          "aws:SourceIp": "203.0.113.0/24"
        },
        "StringEquals": {
          "aws:PrincipalTag/Department": "Finance"
        }
      }
    }
  ]
}
```

---

## ⚖️ Lógica de Avaliação de Permissões

A AWS avalia permissões nesta ordem:

1. **Deny Explícito** → Sempre vence
2. **SCPs** → Limites da organização
3. **Permission Boundaries** → Limites da identidade
4. **Session Policies** → Limites da sessão
5. **Identity-Based Policies** → Permissões diretas
6. **Resource-Based Policies** → Permissões no recurso

**Regra de Ouro:** Por padrão, tudo é negado. É necessário um "Allow" explícito E nenhum "Deny" explícito.

---

## 🎯 Casos de Uso Práticos

### **1. Desenvolvedor com Acesso Limitado**

json

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:Describe*",
        "s3:List*",
        "s3:Get*",
        "dynamodb:Scan",
        "dynamodb:Query",
        "dynamodb:GetItem"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": [
        "*:Delete*",
        "iam:*"
      ],
      "Resource": "*"
    }
  ]
}
```

### **2. Lambda Acessando DynamoDB e S3**

bash

```bash
# Trust policy
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {"Service": "lambda.amazonaws.com"},
    "Action": "sts:AssumeRole"
  }]
}

# Permissions policy
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:UpdateItem"
      ],
      "Resource": "arn:aws:dynamodb:us-east-1:123456789012:table/MinhaTabela"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::meu-bucket/*"
    }
  ]
}
```

### **3. Acesso Cross-Account**

**Conta A (999999999999) - Cria o Role:**

json

```json
// Trust policy do role
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "AWS": "arn:aws:iam::111111111111:root"
    },
    "Action": "sts:AssumeRole"
  }]
}
```

**Conta B (111111111111) - Permite usuários assumirem:**

json

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "sts:AssumeRole",
    "Resource": "arn:aws:iam::999999999999:role/CrossAccountRole"
  }]
}
```

### **4. Acesso Temporário com Condições**

json

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "s3:*",
    "Resource": "arn:aws:s3:::projeto-${aws:username}/*",
    "Condition": {
      "DateGreaterThan": {"aws:CurrentTime": "2025-01-01T00:00:00Z"},
      "DateLessThan": {"aws:CurrentTime": "2025-12-31T23:59:59Z"},
      "IpAddress": {"aws:SourceIp": ["10.0.0.0/8", "172.16.0.0/12"]},
      "Bool": {"aws:SecureTransport": "true"}
    }
  }]
}
```

---

## 🛡️ Melhores Práticas

1. **Princípio do Menor Privilégio** - Dê apenas as permissões necessárias
2. **Use Roles, não Access Keys** - Para aplicações e serviços
3. **Habilite MFA** - Especialmente para usuários privilegiados
4. **Use AWS Organizations + SCPs** - Para governança multi-conta
5. **Rotacione Credenciais** - Regularmente
6. **Use IAM Access Analyzer** - Para identificar acessos públicos
7. **Tag suas Políticas** - Para organização e billing
8. **Condition Keys** - Para controle granular (IP, horário, MFA, tags)
9. **Monitore com CloudTrail** - Todas as chamadas de API
10. **Use Permission Boundaries** - Ao delegar criação de roles/users

---

## 🔍 Ferramentas Úteis

bash

```bash
# Simular política
aws iam simulate-principal-policy \
  --policy-source-arn arn:aws:iam::123456789012:user/joao \
  --action-names s3:GetObject \
  --resource-arns arn:aws:s3:::meu-bucket/arquivo.txt

# Listar permissões efetivas
aws iam get-user-policy --user-name joao --policy-name MinhaPolicy

# Verificar quem pode fazer uma ação
aws iam get-policy-version \
  --policy-arn arn:aws:iam::123456789012:policy/MinhaPolicy \
  --version-id v1

# IAM Access Analyzer
aws accessanalyzer list-findings
```

---

## 📊 Exemplo Completo: Setup Organizacional

bash

```bash
# 1. Criar grupos por função
aws iam create-group --group-name Admins
aws iam create-group --group-name Desenvolvedores
aws iam create-group --group-name Auditores

# 2. Anexar policies aos grupos
aws iam attach-group-policy \
  --group-name Admins \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

aws iam attach-group-policy \
  --group-name Desenvolvedores \
  --policy-arn arn:aws:iam::aws:policy/PowerUserAccess

aws iam attach-group-policy \
  --group-name Auditores \
  --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess

# 3. Criar usuários e adicionar aos grupos
aws iam create-user --user-name maria
aws iam add-user-to-group --user-name maria --group-name Desenvolvedores

# 4. Criar role para aplicação
aws iam create-role --role-name AppRole --assume-role-policy-document file://trust.json
aws iam attach-role-policy --role-name AppRole --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess
```

---

Isso cobre as principais formas de trabalhar com permissões no IAM! Quer que eu aprofunde em algum tópico específico ou mostre exemplos práticos de algum cenário particular?