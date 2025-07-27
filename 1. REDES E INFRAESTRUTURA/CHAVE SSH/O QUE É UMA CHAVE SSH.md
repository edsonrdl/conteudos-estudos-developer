**Chave SSH** (Secure Shell) é um par de credenciais criptográficas usado para autenticar e estabelecer conexões seguras entre um cliente e um servidor via protocolo SSH.

---

## 1. Conceito Básico

- É composta por **duas partes**:
    - **Chave pública**: você instala no servidor; pode ser compartilhada livremente.
    - **Chave privada**: fica guardada com você; **nunca** deve ser divulgada.

> Analogia: pense numa caixa-forte com duas fechaduras. A fechadura pública está no cofre (servidor). Você possui a outra chave (privada). Só quem tem a chave privada consegue abrir o cofre, mesmo que todos possam tentar usar a fechadura pública.

---

## 2. Por que usar

- **Segurança**: elimina o uso de senhas fracas ou repetidas.
- **Automação**: scripts e ferramentas CI/CD conectam-se sem intervenção humana.
- **Controle de acesso**: cada usuário tem seu próprio par de chaves; fácil revogar permissões (basta remover a chave pública).

---

## 3. Como funciona

1. Cliente inicia conexão SSH ao servidor.
2. Servidor envia um **desafio criptográfico** (texto cifrado) usando a chave pública.
3. Cliente decifra o desafio com sua chave privada e devolve a resposta.
4. Servidor verifica se a resposta está correta.
5. Se válido, abre o canal seguro.

---

## 4. Gerando um par de chaves

No seu terminal (Linux/macOS) ou Git Bash (Windows), execute:

```bash
bash
CopiarEditar
ssh-keygen -t rsa -b 4096 -C "seu-email@exemplo.com"

```

- `t rsa`: algoritmo RSA.
- `b 4096`: tamanho em bits (segurança).
- `C`: comentário (opcional, geralmente e‑mail).

Ele irá perguntar onde salvar (`~/.ssh/id_rsa`) e uma **senha de proteção** (passphrase) opcional.

---

## 5. Instalando no servidor

1. Copie a chave pública para o servidor:
    
    ```bash
    bash
    CopiarEditar
    ssh-copy-id usuario@servidor
    
    ```
    
2. Ou manualmente:
    
    ```bash
    bash
    CopiarEditar
    cat ~/.ssh/id_rsa.pub | ssh usuario@servidor "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
    
    ```
    
3. Verifique permissões:
    
    ```bash
    bash
    CopiarEditar
    ssh usuario@servidor
    
    ```
    

---

## 6. Boas práticas

- **Proteja** sempre a chave privada (permissões 600).
- Use **passphrase** para adicionar camada extra.
- Gere um par de chaves **por máquina/usuário**.
- **Revogue** removendo a linha correspondente em `~/.ssh/authorized_keys`.

---

## 7. Exemplos de uso

- **Conexão SSH** simples:
    
    ```bash
    bash
    CopiarEditar
    ssh usuario@servidor
    
    ```
    
- **Git remoto** via SSH:
    
    ```bash
    bash
    CopiarEditar
    git clone git@github.com:usuario/repositorio.git
    
    ```
    

---

Com essas informações, você já consegue entender o que é, por que e como usar uma **chave SSH** com segurança e agilidade.