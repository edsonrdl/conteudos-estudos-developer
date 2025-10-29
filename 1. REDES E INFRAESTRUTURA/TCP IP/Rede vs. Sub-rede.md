A forma mais simples de entender é que a **rede** é o **todo**, enquanto a **sub-rede** é uma **parte** desse todo.

- A **Rede** é o bloco total de endereços IP que você tem disponível. Pense em uma empresa que recebe um grande número de telefones, por exemplo, todos os números que começam com `55-555`. Essa é a sua rede de telefones.
    
- A **Sub-rede** é o que você faz com esses números para organizá-los. Em vez de dar um número aleatório a cada funcionário, você divide o bloco principal por departamento. Por exemplo, todos os números `55-555-1xxx` são para o departamento de Vendas e todos os `55-555-2xxx` são para o de TI. Cada um desses departamentos é uma sub-rede.
    

Em resumo: Você **recebe** uma **rede** e a **divide** em **sub-redes** para gerenciá-la de forma mais eficiente.

---

### Exemplo Real e Como Identificar

Vamos usar um exemplo técnico comum: a rede `192.168.1.0/24`.

#### 1. Identificando a Rede

A **rede** é definida pelo endereço **e** pela máscara de rede. No nosso exemplo:

- **Endereço da Rede:** `192.168.1.0`
    
- **Máscara de Rede:** `/24` (que significa `255.255.255.0`)
    

A combinação desses dois elementos nos diz que a rede inteira abrange todos os endereços de `192.168.1.0` a `192.168.1.255`, um total de 256 endereços.

#### 2. Criando e Identificando as Sub-redes
![[subnet-img-01.png]]
Agora, vamos dividir essa rede em duas sub-redes menores para, por exemplo, separar o tráfego de PCs (`Subnet A`) do tráfego de impressoras (`Subnet B`).

Para dividir a rede, alteramos a máscara. Em vez de `/24`, vamos usar `/25`.

Isso nos dá duas novas sub-redes:

- **Sub-rede A:** `192.168.1.0/25` (com endereços de `192.168.1.0` a `192.168.1.127`)
    
- **Sub-rede B:** `192.168.1.128/25` (com endereços de `192.168.1.128` a `192.168.1.255`)
    

#### Como identificar cada uma delas

A forma mais fácil de diferenciar uma sub-rede da rede principal é olhar para a máscara (`/`):

- Se você tem uma rede principal, como `192.168.1.0/24`, e vê um endereço dentro dela com uma máscara **maior**, como `192.168.1.50/25`, você está olhando para uma **sub-rede**.
    

| Característica          | Rede Principal        | sub-antiredeposição   |
| ----------------------- | --------------------- | --------------------- |
| **Endereço de Exemplo** | `192.168.1.0/24`      | `192.168.1.0/25`      |
| **Tamanho**             | Maior (256 endereços) | Menor (128 endereços) |
| **Máscara**             | `/24`                 | `/25`(número maior)   |

Exportar para as Planilhas

Em resumo, a **rede** é o bloco inicial, e a **sub-rede** é qualquer bloco menor que foi criado a partir daquele inicial. A máscara (`/`) é a chave que indica o tamanho e a natureza de cada um.