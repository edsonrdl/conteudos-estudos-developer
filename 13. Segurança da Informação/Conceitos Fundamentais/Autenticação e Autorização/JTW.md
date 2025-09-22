O JSON Web Token (JWT) é um padrão aberto para autenticação e troca de informações seguras entre diferentes sistemas. Ele é composto por três partes principais: **header**, **payload** e **signature**. Vamos analisar cada um desses componentes e o processo de criptografia e descriptografia:

![[13. Segurança da Informação/img/jwt-structure.png]]

### 1. **Header**

O header do JWT contém informações sobre o tipo de token e o algoritmo usado para assinar a informação. No seu caso, o header tem:

- `"alg": "HS256"`: O algoritmo de hash utilizado é o HMAC com SHA-256. Este é um algoritmo de chave simétrica, ou seja, a mesma chave secreta usada para assinar o token também será usada para validá-lo.
- `"typ": "JWT"`: O tipo de token, que é o JWT.

### 2. **Payload**

O payload contém os dados que estão sendo transmitidos no token, geralmente informações sobre o usuário ou sobre o processo de autenticação. Exemplo:

- `"sub": "123456789"`: O identificador do usuário.
- `"name": "John Doe"`: O nome do usuário.
- `"iat": 1516239022`: O tempo de criação do token, geralmente em formato Unix timestamp.
- `"admin": true`: Um campo que pode indicar se o usuário tem privilégios administrativos.

Esses dados não são criptografados por padrão, então qualquer pessoa que tenha acesso ao token pode visualizá-los. Porém, se você usar uma chave secreta para assinar o token, eles não podem ser modificados sem invalidar a assinatura.

### 3. **Signature**

A assinatura serve para garantir que o conteúdo do token não foi alterado. Ela é gerada usando a chave secreta e o algoritmo especificado no header (neste caso, HMAC com SHA-256). A assinatura é gerada da seguinte forma:

- Primeiramente, a parte do **header** e a parte do **payload** do JWT são codificadas em Base64Url.
- Em seguida, essas duas partes são concatenadas com um ponto (.) no meio, formando uma string.
- A chave secreta é usada para aplicar o algoritmo HMACSHA256 sobre essa string concatenada, gerando o valor da assinatura.
- A assinatura é a última parte do JWT.

### Processo de Criptografia e Descriptografia

1. **Criptografia:**
    
    - A parte **header** e **payload** são codificadas em Base64Url.
    - O algoritmo de hash (HMACSHA256) é aplicado com a chave secreta para gerar a **signature**.
    - O JWT final é formado pelas três partes codificadas: header, payload e signature, separados por pontos.
2. **Descriptografia e Validação:**
    
    - Quando um servidor recebe um JWT, ele pode verificar a assinatura para garantir que o conteúdo não foi alterado.
    - O servidor primeiro separa as três partes do JWT (header, payload e signature).
    - Ele reconstrói a string concatenando novamente o header e o payload.
    - A chave secreta e o algoritmo HMACSHA256 são usados para gerar uma nova assinatura.
    - Se a assinatura gerada corresponder à assinatura presente no token, o JWT é válido, e o servidor pode confiar nas informações contidas no payload.
    
    **Fluxo de Validação:**
    
    - **Entrada**: O JWT é enviado para o servidor (normalmente no cabeçalho Authorization).
    - **Processamento**: O servidor decodifica e valida o token verificando a assinatura.
    - **Saída**: Se a assinatura for válida, o servidor processa o payload e permite ou nega a ação solicitada.

### Em resumo:

- **Header**: Contém informações sobre o algoritmo e tipo de token.
- **Payload**: Contém os dados (não criptografados).
- **Signature**: Garante que o token não foi alterado e é gerada usando uma chave secreta com o algoritmo de hash.

Esse processo de assinatura e validação é fundamental para garantir que um token JWT seja seguro e não seja manipulado entre os sistemas.

Sim, o **JWT** (JSON Web Token) pode ser uma excelente solução para trabalhar com **autenticação** e **autorização** em sistemas distribuídos, como no caso de um sistema com um único frontend, múltiplos perfis, papéis e serviços. O JWT pode ser integrado a mecanismos de controle de acesso como **RBAC** (Role-Based Access Control) ou **ABAC** (Attribute-Based Access Control), dependendo das necessidades da sua aplicação.