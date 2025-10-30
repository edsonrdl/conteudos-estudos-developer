## O Fluxo e as Permissões Necessárias

Neste cenário, precisamos de duas Funções (Roles) diferentes e duas políticas de permissão:

|**Componente**|**Função (Role)**|**Permissão (Policy)**|
|---|---|---|
|**AWS Lambda**|**Role de Execução da Lambda**|Permite: 1. A Lambda **executar** e enviar logs. 2. **Chamar a API** do AWS Glue (`glue:StartJobRun`). 3. **Passar a Role do Glue** para o serviço Glue (`iam:PassRole`).|
|**AWS Glue**|**Role de Execução do Glue**|Permite: 1. O Glue **acessar o S3** (ler e escrever os dados). 2. O Glue **escrever logs** no CloudWatch.|

### 🔍 Exemplo de JSON: A Permissão Crítica `iam:PassRole`

O ponto mais confuso é o `Resource` para a ação `iam:PassRole` na Política de Permissões da Lambda. Ele deve ser o ARN (Amazon Resource Name) do **Role do Glue**, garantindo o princípio do Mínimo Privilégio.

Aqui está o JSON da política que você anexaria ao **Role de Execução da Lambda**:

JSON

```
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
            "Action": "glue:StartJobRun",
            "Resource": "arn:aws:glue:us-east-1:123456789012:job/meu-job-particionado" 
        },
        {
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::123456789012:role/role-execucao-glue"
        }
    ]
}
```

|**Elemento do JSON**|**Explicação no Contexto Lambda/Glue**|
|---|---|
|`glue:StartJobRun`|**O que** a Lambda pode fazer: iniciar uma execução do Job no Glue.|
|`Resource` (`...job/meu-job-particionado`)|**Onde** a ação se aplica: a Lambda só pode iniciar **este Job específico** no Glue (ARN do Glue Job).|
|`iam:PassRole`|A permissão mais importante: permite à Lambda **passar** o Role de Execução do Glue para o serviço AWS Glue.|
|`Resource` (`...role/role-execucao-glue`)|**Onde** a ação se aplica: a Lambda só pode passar **este Role específico** do Glue. Isso evita que ela use um Role com permissões excessivas.|

---

## 2. Exemplo Completo em Terraform

O código a seguir cria a infraestrutura completa (Roles, Policies, S3, Glue Job e Lambda Function) usando Terraform.

### Pré-requisitos (Arquivo `main.tf`)

Para simplificar o exemplo, vamos definir as variáveis e um bucket S3.

Terraform

```
# Variáveis e Nomeclaturas
locals {
  job_name        = "meu-job-particionado"
  bucket_name     = "meu-bucket-dados-processamento-terraform-12345" # Deve ser único globalmente
  region          = "us-east-1"
  account_id      = data.aws_caller_identity.current.account_id
}

# Dados da conta atual
data "aws_caller_identity" "current" {}

# 1. Bucket S3 para Dados e Scripts do Glue
resource "aws_s3_bucket" "data_bucket" {
  bucket = local.bucket_name
}
# Necessário para evitar o erro "Bucket not empty" na destruição
resource "aws_s3_bucket_acl" "data_bucket_acl" {
  bucket = aws_s3_bucket.data_bucket.id
  acl    = "private"
}

# Upload de um script de exemplo (pode ser um script Python simples)
resource "aws_s3_object" "glue_script" {
  bucket = aws_s3_bucket.data_bucket.id
  key    = "scripts/${local.job_name}.py"
  source = "glue_script.py" # O arquivo deve existir na mesma pasta do terraform
  etag   = filemd5("glue_script.py")
}
```

### 2. Role e Policy para o AWS Glue

Esta Role é assumida pelo serviço `glue.amazonaws.com` para realizar o trabalho ETL.

Terraform

```
# 2.1. Política de Confiança do GLUE (Quem pode assumir o Role)
resource "aws_iam_role" "glue_execution_role" {
  name               = "role-execucao-glue"
  assume_role_policy = jsonencode({
    Version = "2012-10-17",
    Statement = [{
      Effect    = "Allow",
      Principal = { Service = "glue.amazonaws.com" },
      Action    = "sts:AssumeRole",
    }]
  })
}

# 2.2. Política de Permissões do GLUE (O que o Glue pode fazer)
resource "aws_iam_policy" "glue_policy" {
  name        = "policy-permissao-glue"
  description = "Permite ao Glue acessar S3, CloudWatch e rodar o job."

  policy = jsonencode({
    Version = "2012-10-17",
    Statement = [
      { # Permissão de Logs no CloudWatch
        Effect = "Allow",
        Action = [
          "logs:CreateLogGroup",
          "logs:CreateLogStream",
          "logs:PutLogEvents",
          "logs:AssociateLogStream",
          "logs:PutLogEvents"
        ],
        Resource = "arn:aws:logs:*:*:log-group:/aws-glue/jobs/*"
      },
      { # Permissão de Acesso ao S3 para os dados e o script
        Effect = "Allow",
        Action = [
          "s3:GetObject",
          "s3:PutObject",
          "s3:DeleteObject",
          "s3:ListBucket"
        ],
        Resource = [
          aws_s3_bucket.data_bucket.arn,
          "${aws_s3_bucket.data_bucket.arn}/*" # Permite acesso a todos os objetos no bucket
        ]
      }
    ]
  })
}

# 2.3. Anexar a política ao Role
resource "aws_iam_role_policy_attachment" "glue_attach" {
  role       = aws_iam_role.glue_execution_role.name
  policy_arn = aws_iam_policy.glue_policy.arn
}
```

### 3. Role e Policy para a AWS Lambda

Esta Role é assumida pelo serviço `lambda.amazonaws.com`. É aqui que incluímos as permissões `glue:StartJobRun` e `iam:PassRole`.

Terraform

```
# 3.1. Política de Confiança da LAMBDA (Quem pode assumir o Role)
resource "aws_iam_role" "lambda_execution_role" {
  name               = "role-execucao-lambda"
  assume_role_policy = jsonencode({
    Version = "2012-10-17",
    Statement = [{
      Effect    = "Allow",
      Principal = { Service = "lambda.amazonaws.com" },
      Action    = "sts:AssumeRole",
    }]
  })
}

# 3.2. Política de Permissões da LAMBDA (O que a Lambda pode fazer)
resource "aws_iam_policy" "lambda_glue_policy" {
  name        = "policy-lambda-chama-glue"
  description = "Permite a Lambda iniciar o Job Glue e passar o Role do Glue."

  policy = jsonencode({
    Version = "2012-10-17",
    Statement = [
      { # Permissão de Logs no CloudWatch (padrão para Lambda)
        Effect = "Allow",
        Action = [
          "logs:CreateLogGroup",
          "logs:CreateLogStream",
          "logs:PutLogEvents",
        ],
        Resource = "arn:aws:logs:*:*:log-group:/aws/lambda/*:*"
      },
      { # Permissão para Iniciar o Job Glue
        Effect = "Allow",
        Action = "glue:StartJobRun",
        Resource = "arn:aws:glue:${local.region}:${local.account_id}:job/${local.job_name}"
      },
      { # Permissão CRÍTICA: Passar o Role do Glue para o serviço Glue
        Effect = "Allow",
        Action = "iam:PassRole",
        # O Recurso aqui é o ARN EXATO do Role que o Glue usará!
        Resource = aws_iam_role.glue_execution_role.arn
      }
    ]
  })
}

# 3.3. Anexar a política ao Role
resource "aws_iam_role_policy_attachment" "lambda_attach" {
  role       = aws_iam_role.lambda_execution_role.name
  policy_arn = aws_iam_policy.lambda_glue_policy.arn
}
```

### 4. Definição do Glue Job e da Lambda

Terraform

```
# 4.1. Definição do Glue Job
resource "aws_glue_job" "partitioned_job" {
  name     = local.job_name
  role_arn = aws_iam_role.glue_execution_role.arn # O Glue usará este Role
  
  command {
    script_location = "s3://${aws_s3_bucket.data_bucket.id}/${aws_s3_object.glue_script.key}"
    python_version  = 3
  }
  
  default_arguments = {
    "--TempDir" = "s3://${aws_s3_bucket.data_bucket.id}/temp/"
  }
  
  max_capacity = 0.0625 # Mínimo possível para teste
}

# 4.2. Definição da Lambda Function (O código Python da Lambda)
resource "aws_lambda_function" "glue_starter_lambda" {
  function_name    = "start-glue-job-lambda"
  handler          = "index.lambda_handler"
  runtime          = "python3.9"
  role             = aws_iam_role.lambda_execution_role.arn # A Lambda usará este Role

  # Arquivo fictício que simula a lógica da Lambda
  filename         = "lambda_starter.zip" 
  source_code_hash = filebase64sha256("lambda_starter.zip")
}
```

### 5. Conteúdo do Código

Para que o Terraform funcione, você precisaria de dois arquivos simples na mesma pasta:

1. **`glue_script.py` (Script do Glue, Exemplo Simples):**
    
    Python
    
    ```
    import sys
    from awsglue.utils import getResolvedOptions
    
    args = getResolvedOptions(sys.argv, ['JOB_NAME'])
    
    print(f"Starting Glue Job: {args['JOB_NAME']}")
    # 
    # **Aqui estaria a lógica para ler os dados particionados do S3
    # **Ex: spark.read.parquet("s3://meu-bucket-dados/raw/")...
    # 
    print("Glue Job Finished Successfully")
    ```
    
2. **`lambda_starter.py` (Lógica da Lambda, depois zipado para `lambda_starter.zip`):**
    
    Python
    
    ```
    import boto3
    import json
    
    def lambda_handler(event, context):
        glue_client = boto3.client('glue')
        job_name = "meu-job-particionado" 
    
        try:
            response = glue_client.start_job_run(
                JobName=job_name,
                # Argumentos passados para o script Glue (opcional)
                Arguments={
                    '--input_path': 's3://meu-bucket-dados/raw/ano=2025/mes=10/dia=29/' 
                }
            )
            print(f"Glue Job started: {response['JobRunId']}")
            return {
                'statusCode': 200,
                'body': json.dumps(f"Job iniciado com sucesso: {response['JobRunId']}")
            }
        except Exception as e:
            print(f"Erro ao iniciar o Job Glue: {e}")
            raise e
    
    # Para o Terraform, você precisaria zipar este arquivo: zip lambda_starter.zip i
    ```