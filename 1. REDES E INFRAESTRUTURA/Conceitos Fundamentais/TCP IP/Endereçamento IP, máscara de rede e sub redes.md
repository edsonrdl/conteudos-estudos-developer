.

---

## 💡 Conceitos Essenciais

|Termo|O que é|Exemplo Visual|
|---|---|---|
|**Endereço IP**|É a identidade numérica única de um dispositivo em uma rede. No IPv4, é composto por 32 bits (4 octetos), separados por pontos.|**192.168.1.10**|
|**Máscara de Rede**|Define qual parte do Endereço IP identifica a **Rede** e qual parte identifica o **Host** (dispositivo). É usada para saber se o IP está na mesma rede.|**255.255.255.0**|
|**Sub-rede**|É uma divisão (segmentação) de uma rede maior. Serve para gerenciar melhor o tráfego, a segurança e a utilização dos endereços IP.|Uma rede grande é dividida em redes menores como `192.168.1.0/24` e `192.168.2.0/24`.|
|**Notação CIDR**|É uma forma simplificada de representar a Máscara de Rede, indicando o número de bits que pertencem à parte da rede.|**`/24`** (que equivale a `255.255.255.0`)|

Exportar para as Planilhas

---

## 🎯 Exemplo Completo e Cálculo de Sub-rede

Vamos usar um exemplo para entender como determinar a rede, o broadcast e os hosts.

### Cenário de Exemplo

- **Endereço IP do Dispositivo (Host):** `192.168.10.150`
    
- **Máscara de Sub-rede (CIDR):** `/26`
    

### Passo 1: Entender a Máscara em Binário

A notação `/26` significa que **26 bits** são dedicados à porção da **Rede**, e os bits restantes são dedicados à porção do **Host**.

- Total de bits no IPv4: **32**
    
- Bits de Rede: **26**
    
- Bits de Host: 32−26=∗∗6**
    

**Máscara `/26` em binário:**

255![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMinYMin%20slice"><path%20d="M0%206l6-6h17c12.688%200%2019.313.3%2020%201%204%204%207.313%208.3%2010%2013
%2035.313%2051.3%2080.813%2093.8%20136.5%20127.5%2055.688%2033.7%20117.188%2055.8%20184.5%2066.5.688
%200%202%20.3%204%201%2018.688%202.7%2076%204.3%20172%205h399450v120H429l-6-1c-124.688-8-235-61.7
-331-161C60.687%20138.7%2032.312%2099.3%207%2054L0%2041V6z"></path></svg>)![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMidYMin%20slice"><path%20d="M199572%20214
c100.7%208.3%20195.3%2044%20280%20108%2055.3%2042%20101.7%2093%20139%20153l9%2014c2.7-4%205.7-8.7%209-14
%2053.3-86.7%20123.7-153%20211-199%2066.7-36%20137.3-56.3%20212-62h199568v120H200432c-178.3
%2011.7-311.7%2078.3-403%20201-6%208-9.7%2012-11%2012-.7.7-6.7%201-18%201s-17.3-.3-18-1c-1.3%200
-5-4-11-12-44.7-59.3-101.3-106.3-170-141s-145.3-54.3-229-60H0V214z"></path></svg>)![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMaxYMin%20slice"><path%20d="M399994%200l6%206v35l-6%2011c-56%20104-135.3%20181.3-238%20232-57.3
%2028.7-117%2045-179%2050H-300V214h399897c43.3-7%2081-15%20113-26%20100.7-33%20179.7-91%20237
-174%202.7-5%206-9%2010-13%20.7-1%207.3-1%2020-1h17z"></path></svg>)11111111​​.255![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMinYMin%20slice"><path%20d="M0%206l6-6h17c12.688%200%2019.313.3%2020%201%204%204%207.313%208.3%2010%2013
%2035.313%2051.3%2080.813%2093.8%20136.5%20127.5%2055.688%2033.7%20117.188%2055.8%20184.5%2066.5.688
%200%202%20.3%204%201%2018.688%202.7%2076%204.3%20172%205h399450v120H429l-6-1c-124.688-8-235-61.7
-331-161C60.687%20138.7%2032.312%2099.3%207%2054L0%2041V6z"></path></svg>)![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMidYMin%20slice"><path%20d="M199572%20214
c100.7%208.3%20195.3%2044%20280%20108%2055.3%2042%20101.7%2093%20139%20153l9%2014c2.7-4%205.7-8.7%209-14
%2053.3-86.7%20123.7-153%20211-199%2066.7-36%20137.3-56.3%20212-62h199568v120H200432c-178.3
%2011.7-311.7%2078.3-403%20201-6%208-9.7%2012-11%2012-.7.7-6.7%201-18%201s-17.3-.3-18-1c-1.3%200
-5-4-11-12-44.7-59.3-101.3-106.3-170-141s-145.3-54.3-229-60H0V214z"></path></svg>)![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMaxYMin%20slice"><path%20d="M399994%200l6%206v35l-6%2011c-56%20104-135.3%20181.3-238%20232-57.3
%2028.7-117%2045-179%2050H-300V214h399897c43.3-7%2081-15%20113-26%20100.7-33%20179.7-91%20237
-174%202.7-5%206-9%2010-13%20.7-1%207.3-1%2020-1h17z"></path></svg>)11111111​​.255![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMinYMin%20slice"><path%20d="M0%206l6-6h17c12.688%200%2019.313.3%2020%201%204%204%207.313%208.3%2010%2013
%2035.313%2051.3%2080.813%2093.8%20136.5%20127.5%2055.688%2033.7%20117.188%2055.8%20184.5%2066.5.688
%200%202%20.3%204%201%2018.688%202.7%2076%204.3%20172%205h399450v120H429l-6-1c-124.688-8-235-61.7
-331-161C60.687%20138.7%2032.312%2099.3%207%2054L0%2041V6z"></path></svg>)![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMidYMin%20slice"><path%20d="M199572%20214
c100.7%208.3%20195.3%2044%20280%20108%2055.3%2042%20101.7%2093%20139%20153l9%2014c2.7-4%205.7-8.7%209-14
%2053.3-86.7%20123.7-153%20211-199%2066.7-36%20137.3-56.3%20212-62h199568v120H200432c-178.3
%2011.7-311.7%2078.3-403%20201-6%208-9.7%2012-11%2012-.7.7-6.7%201-18%201s-17.3-.3-18-1c-1.3%200
-5-4-11-12-44.7-59.3-101.3-106.3-170-141s-145.3-54.3-229-60H0V214z"></path></svg>)![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMaxYMin%20slice"><path%20d="M399994%200l6%206v35l-6%2011c-56%20104-135.3%20181.3-238%20232-57.3
%2028.7-117%2045-179%2050H-300V214h399897c43.3-7%2081-15%20113-26%20100.7-33%20179.7-91%20237
-174%202.7-5%206-9%2010-13%20.7-1%207.3-1%2020-1h17z"></path></svg>)11111111​​.192![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMinYMin%20slice"><path%20d="M0%206l6-6h17c12.688%200%2019.313.3%2020%201%204%204%207.313%208.3%2010%2013
%2035.313%2051.3%2080.813%2093.8%20136.5%20127.5%2055.688%2033.7%20117.188%2055.8%20184.5%2066.5.688
%200%202%20.3%204%201%2018.688%202.7%2076%204.3%20172%205h399450v120H429l-6-1c-124.688-8-235-61.7
-331-161C60.687%20138.7%2032.312%2099.3%207%2054L0%2041V6z"></path></svg>)![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMidYMin%20slice"><path%20d="M199572%20214
c100.7%208.3%20195.3%2044%20280%20108%2055.3%2042%20101.7%2093%20139%20153l9%2014c2.7-4%205.7-8.7%209-14
%2053.3-86.7%20123.7-153%20211-199%2066.7-36%20137.3-56.3%20212-62h199568v120H200432c-178.3
%2011.7-311.7%2078.3-403%20201-6%208-9.7%2012-11%2012-.7.7-6.7%201-18%201s-17.3-.3-18-1c-1.3%200
-5-4-11-12-44.7-59.3-101.3-106.3-170-141s-145.3-54.3-229-60H0V214z"></path></svg>)![](data:image/svg+xml;utf8,<svg%20xmlns="http://www.w3.org/2000/svg"%20width="400em"%20height="0.548em"%20viewBox="0%200%20400000%20548"%20preserveAspectRatio="xMaxYMin%20slice"><path%20d="M399994%200l6%206v35l-6%2011c-56%20104-135.3%20181.3-238%20232-57.3
%2028.7-117%2045-179%2050H-300V214h399897c43.3-7%2081-15%20113-26%20100.7-33%20179.7-91%20237
-174%202.7-5%206-9%2010-13%20.7-1%207.3-1%2020-1h17z"></path></svg>)11000000​​

- **Máscara de Sub-rede em Decimal:** **255.255.255.192**
    

---

### Passo 2: Calcular a Rede (Endereço da Sub-rede)

Para encontrar o endereço da sub-rede, você faz uma operação lógica **AND** bit a bit entre o Endereço IP e a Máscara de Rede.

|Octeto|IP (`192.168.10.150`)|Máscara (`255.255.255.192`)|Rede (AND)|
|---|---|---|---|
|1º|`192` (11000000)|`255` (11111111)|`192` (11000000)|
|2º|`168` (10101000)|`255` (11111111)|`168` (10101000)|
|3º|`10` (00001010)|`255` (11111111)|`10` (00001010)|
|**4º**|**`150` (10010110)**|**`192` (11000000)**|**`128` (10000000)**|

Exportar para as Planilhas

- **Endereço da Rede (Sub-rede):** **192.168.10.128**
    

---

### Passo 3: Determinar o Intervalo de Endereços (Hosts e Broadcast)

Com base nos **6 bits de Host** (32−26), podemos calcular:

1. **Número de Endereços Úteis:** 2Bits de Host−2
    
    - 26−2=64−2=∗∗62 Enderec¸​os uˊteis** (Os -2 são para Rede e Broadcast)
        
2. **Tamanho do Bloco (Incremento):** 2Bits de Host
    
    - 26=∗∗64** (Este é o salto entre sub-redes)
        

A partir do endereço da Sub-rede (`192.168.10.128`), a próxima sub-rede será `192.168.10.128 + 64 = 192.168.10.192`.

O intervalo da nossa sub-rede é:

- **Endereço da Sub-rede (Rede):** 192.168.10.128 (É sempre o primeiro endereço, todos os bits de host são **0**s)
    
- **Primeiro Host Válido:** 192.168.10.128+1=192.168.10.129
    
- **Último Host Válido:** Próxima Rede - 2 = 192.168.10.192−2=192.168.10.190
    
- **Endereço de Broadcast:** Próxima Rede - 1 = 192.168.10.192−1=192.168.10.191 (É sempre o último endereço, todos os bits de host são **1**s)
    

---

## ✅ Resumo do Exemplo

**Para o IP `192.168.10.150` com Máscara `/26` (255.255.255.192):**

|Parâmetro|Valor Encontrado|
|---|---|
|**Endereço da Sub-rede**|**192.168.10.128**|
|**Primeiro Host Válido**|**192.168.10.129**|
|**IP do Dispositivo**|**192.168.10.150** (Está neste intervalo!)|
|**Último Host Válido**|**192.168.10.190**|
|**Endereço de Broadcast**|**192.168.10.191**|
|**Total de Hosts Úteis**|**62**|

Exportar para as Planilhas

O seu IP `192.168.10.150` está na sub-rede que começa em `192.168.10.128`.

---

## 🅰️ Classe A: Redes Enormes

A Classe A é usada para redes muito grandes, onde o primeiro octeto define a rede e os três restantes definem os hosts.

### Exemplo de Classe A

|**Parâmetro**|**Valor**|
|---|---|
|**IP de Exemplo**|$\mathbf{10.200.50.3}$|
|**Máscara Padrão (CIDR)**|$\mathbf{/8}$|
|**Máscara Padrão (Decimal)**|$\mathbf{255.0.0.0}$|

1. **Divisão Rede/Host:**
    
    - **Rede:** 1º Octeto (**10**).
        
    - **Host:** 2º, 3º e 4º Octetos ($\mathbf{200.50.3}$).
        
2. **Cálculo (Operação AND):**
    
    - $\text{IP}: \mathbf{10} . 200 . 50 . 3$
        
    - $\text{Máscara}: \mathbf{255} . 0 . 0 . 0$
        
    - $\text{Endereço da Rede}: \mathbf{10} . 0 . 0 . 0$
        
3. **Endereços (Intervalo):**
    
    - **Endereço da Rede:** $\mathbf{10.0.0.0}$
        
    - **Primeiro Host Válido:** $10.0.0.1$
        
    - **Último Host Válido:** $10.255.255.254$
        
    - **Broadcast:** $\mathbf{10.255.255.255}$
        
    - **Total de Hosts Úteis:** $2^{24} - 2 \approx 16.7 \text{ Milhões!}$ (24 bits de host)
        

---

## 🅱️ Classe B: Redes Médias a Grandes

A Classe B é usada para redes de tamanho médio a grande, onde os dois primeiros octetos definem a rede e os dois restantes definem os hosts.

### Exemplo de Classe B

|**Parâmetro**|**Valor**|
|---|---|
|**IP de Exemplo**|$\mathbf{172.16.20.100}$|
|**Máscara Padrão (CIDR)**|$\mathbf{/16}$|
|**Máscara Padrão (Decimal)**|$\mathbf{255.255.0.0}$|

1. **Divisão Rede/Host:**
    
    - **Rede:** 1º e 2º Octetos ($\mathbf{172.16}$).
        
    - **Host:** 3º e 4º Octetos ($\mathbf{20.100}$).
        
2. **Cálculo (Operação AND):**
    
    - $\text{IP}: \mathbf{172.16} . 20 . 100$
        
    - $\text{Máscara}: \mathbf{255.255} . 0 . 0$
        
    - $\text{Endereço da Rede}: \mathbf{172.16} . 0 . 0$
        
3. **Endereços (Intervalo):**
    
    - **Endereço da Rede:** $\mathbf{172.16.0.0}$
        
    - **Primeiro Host Válido:** $172.16.0.1$
        
    - **Último Host Válido:** $172.16.255.254$
        
    - **Broadcast:** $\mathbf{172.16.255.255}$
        
    - **Total de Hosts Úteis:** $2^{16} - 2 = 65.534 \text{ Hosts}$ (16 bits de host)
        

---

## 🇨 Classe C: Redes Pequenas

A Classe C é a mais comum para redes locais pequenas, onde os três primeiros octetos definem a rede e o último octeto define os hosts.

### Exemplo de Classe C

|**Parâmetro**|**Valor**|
|---|---|
|**IP de Exemplo**|$\mathbf{192.168.1.5}$|
|**Máscara Padrão (CIDR)**|$\mathbf{/24}$|
|**Máscara Padrão (Decimal)**|$\mathbf{255.255.255.0}$|

1. **Divisão Rede/Host:**
    
    - **Rede:** 1º, 2º e 3º Octetos ($\mathbf{192.168.1}$).
        
    - **Host:** 4º Octeto ($\mathbf{5}$).
        
2. **Cálculo (Operação AND):**
    
    - $\text{IP}: \mathbf{192.168.1} . 5$
        
    - $\text{Máscara}: \mathbf{255.255.255} . 0$
        
    - $\text{Endereço da Rede}: \mathbf{192.168.1} . 0$
        
3. **Endereços (Intervalo):**
    
    - **Endereço da Rede:** $\mathbf{192.168.1.0}$
        
    - **Primeiro Host Válido:** $192.168.1.1$
        
    - **Último Host Válido:** $192.168.1.254$
        
    - **Broadcast:** $\mathbf{192.168.1.255}$
        
    - **Total de Hosts Úteis:** $2^{8} - 2 = 254 \text{ Hosts}$ (8 bits de host)
        

---

### 🧠 Bônus: O Poder da Sub-rede (VLSM)

O verdadeiro poder e complexidade vêm quando você "pega emprestado" bits da porção de **Host** para criar **Sub-redes** (como no nosso primeiro exemplo com o `/26`).

- Se você usar um IP de Classe C (`192.168.1.X`) e aplicar uma máscara `/26` (255.255.255.192), você está **pegando 2 bits emprestados** do host (os 24 bits da rede + 2 bits emprestados = 26 bits de rede).
    
- Com 2 bits emprestados, você pode criar $2^2 = **4 \text{ sub-redes}$**, cada uma com **62 hosts úteis** ($2^{6}-2$).
    

---

## 🧮 Como a Máscara é Definida

A definição da máscara segue dois princípios interligados:

1. **Regra do Bit:** A Máscara de Rede é uma sequência de **1s** (que definem a rede) seguida por uma sequência de **0s** (que definem o host). Você não pode ter 0s no meio dos 1s.
    
2. **Necessidade de Endereços:** O fator que determina quantos 1s e 0s você usará é a sua necessidade por um lado de **sub-redes** e por outro de **hosts** (dispositivos) em cada sub-rede.
    

---

### Passo 1: O Ponto de Partida (CIDR)

A maneira mais fácil de definir uma máscara é pensar em termos de **Notação CIDR** (Classless Inter-Domain Routing).

O CIDR é o número que aparece depois da barra (`/`).

$$\text{Máscara} = \mathbf{/N}$$

Onde $N$ é o **número de bits ligados (1s)** a partir da esquerda.

- **Bits de Rede:** $N$
    
- **Bits de Host:** $32 - N$
    

|**CIDR**|**Máscara (Binário)**|**Máscara (Decimal)**|**Exemplo de Rede**|**Hosts Úteis (232−N−2)**|
|---|---|---|---|---|
|$/8$|11111111.00000000.00000000.00000000|**255.0.0.0**|Classe A|$\approx 16.7$ Milhões|
|$/16$|11111111.11111111.00000000.00000000|**255.255.0.0**|Classe B|$65.534$|
|$/24$|11111111.11111111.11111111.00000000|**255.255.255.0**|Classe C|$254$|

---

### Passo 2: A Definição Pela Necessidade (Sub-redes)

A máscara é definida no momento em que você decide **dividir** uma rede maior em sub-redes menores. Você faz isso **pegando emprestado bits da porção de Host** (transformando 0s em 1s na Máscara).

Vamos usar um exemplo comum: você tem uma rede de Classe C padrão (`/24`) e precisa dividi-la em 8 sub-redes.

#### 1. Necessidade de Sub-redes

- **Objetivo:** Criar 8 sub-redes.
    
- **Fórmula:** O número de sub-redes é dado por $2^{\text{Bits Emprestados}}$.
    
- **Cálculo:** Para ter 8 sub-redes, precisamos de $2^{\mathbf{3}} = 8$.
    
- **Conclusão:** Você precisa **pegar 3 bits emprestados** da porção de Host.
    

#### 2. Cálculo da Nova Máscara

Se a máscara inicial era `/24`, ao pegar 3 bits emprestados, a nova máscara é:

$$\text{Novo CIDR} = 24 + 3 = \mathbf{/27}$$

#### 3. Conversão para Decimal

No quarto octeto, tínhamos 8 zeros. Agora, 3 se tornaram '1's:

- **Binário do 4º Octeto:** $\mathbf{111}00000$
    
- **Cálculo Decimal:** $128 + 64 + 32 = \mathbf{224}$
    

|**Parâmetro**|**Valor**|
|---|---|
|**Nova Máscara (Decimal)**|**255.255.255.224**|
|**Bits de Rede (CIDR)**|$/27$|
|**Bits de Host Restantes**|$32 - 27 = 5$|

#### 4. Resultado para Hosts

Com 5 bits de Host restantes, você terá $2^5 - 2 = 32 - 2 = **30 \text{ hosts úteis}$** em cada uma das 8 sub-redes.

$$\text{Em resumo: a máscara (e o CIDR) é definida pelo equilíbrio entre a quantidade de hosts que você precisa em cada sub-rede e a quantidade de sub-redes que você precisa criar.}$$## 🛠️ Cenário Prático para Definição da Máscara

Imagine que você está projetando a rede para um novo escritório e recebeu o bloco de endereços **`192.168.5.0/24`** (uma rede de Classe C padrão).

### Requisitos da Rede

1. Você precisa dividir esta rede principal em, no mínimo, **5 sub-redes** distintas (para os setores Financeiro, Marketing, TI, etc.).
    
2. Cada sub-rede deve suportar, no mínimo, **25 dispositivos** (hosts úteis).
    

### Passo 1: Definir os Bits de Sub-rede (Atendendo à Necessidade de Sub-redes)

O objetivo é criar **5 sub-redes**. Usamos a fórmula $2^{\text{Bits de Sub-rede}}$ para encontrar o número mínimo de bits necessários.

- $2^1 = 2$ (Pouco)
    
- $2^2 = 4$ (Pouco)
    
- $2^{\mathbf{3}} = \mathbf{8}$ (Suficiente, 8 sub-redes disponíveis)
    

**Conclusão:** Precisamos pegar emprestado **3 bits** da porção de Host.

### Passo 2: Calcular a Nova Máscara (CIDR)

A rede inicial era `/24`. Adicionamos os 3 bits que pegamos emprestado:

$$\text{Novo CIDR} = 24 + 3 = \mathbf{/27}$$

### Passo 3: Verificar a Necessidade de Hosts

Agora, precisamos garantir que o novo `/27` atende ao segundo requisito: **pelo menos 25 hosts úteis** em cada sub-rede.

1. Bits de Host Restantes:
    
    $$\text{Bits de Host} = 32 - \text{Novo CIDR} = 32 - 27 = \mathbf{5 \text{ bits}}$$
    
2. Total de Hosts Úteis:
    
    $$\text{Hosts Úteis} = 2^{\text{Bits de Host}} - 2$$
    
    $$\text{Hosts Úteis} = 2^5 - 2 = 32 - 2 = \mathbf{30 \text{ hosts úteis}}$$
    

**Verificação:** 30 hosts úteis é **maior** do que os 25 hosts úteis exigidos. **O `/27` é a máscara ideal!**

### Passo 4: Definir a Máscara em Decimal

Pegamos 3 bits emprestados no último octeto (onde estavam todos os zeros).

- **4º Octeto em Binário:** $\mathbf{111}00000$ (3 uns + 5 zeros)
    
- **4º Octeto em Decimal:** $128 + 64 + 32 = \mathbf{224}$
    

---

## ✅ A Resposta (Máscara Definida)

Para atender aos requisitos de **5 sub-redes** (resultando em 8 sub-redes disponíveis) e **25 hosts úteis** (resultando em 30 hosts úteis por sub-rede), a máscara ideal é:

|**Tipo**|**Máscara Definida**|
|---|---|
|**CIDR**|$\mathbf{/27}$|
|**Decimal**|$\mathbf{255.255.255.224}$|

### Bônus: Divisão das Sub-redes

Com essa máscara `/27`, o bloco de rede ($2^{5} = 32$) será de 32 em 32. As 8 sub-redes criadas seriam:

|**Sub-rede (Setor)**|**Endereço da Sub-rede (Rede)**|**Intervalo de Hosts Válidos**|**Endereço de Broadcast**|
|---|---|---|---|
|**1 (Financeiro)**|**192.168.5.0**|192.168.5.1 até 192.168.5.30|192.168.5.31|
|**2 (Marketing)**|**192.168.5.32**|192.168.5.33 até 192.168.5.62|192.168.5.63|
|**3 (TI)**|**192.168.5.64**|192.168.5.65 até 192.168.5.94|192.168.5.95|
|**...e mais 5 sub-redes...**|**192.168.5.96**, **192.168.5.128**, etc.|...|...|

---

Este exemplo mostra que a máscara não é escolhida aleatoriamente, mas sim um resultado de um cálculo que **satisfaz os requisitos de design da rede** (quantas sub-redes e quantos hosts).

## 🏢 Cenário Prático com Classe B

Você é responsável por gerenciar a rede de uma grande universidade e recebeu o seguinte bloco de endereços: **`172.16.0.0/16`** (Máscara Padrão $255.255.0.0$).

### Requisitos da Rede

1. Você precisa dividir esta rede principal em, no mínimo, **20 sub-redes** distintas (para diferentes campi ou departamentos).
    
2. Cada sub-rede deve suportar, no mínimo, **1000 dispositivos** (hosts úteis) para cobrir laboratórios, salas de aula e Wi-Fi.
    

### Passo 1: Definir os Bits de Sub-rede (Atendendo a 20 Sub-redes)

O objetivo é ter, no mínimo, **20 sub-redes**. Usamos a fórmula $2^{\text{Bits de Sub-rede}}$:

- $2^4 = 16$ (Pouco)
    
- $2^{\mathbf{5}} = \mathbf{32}$ (Suficiente, 32 sub-redes disponíveis)
    

**Conclusão:** Precisamos pegar emprestado **5 bits** da porção de Host.

### Passo 2: Calcular a Nova Máscara (CIDR)

A rede inicial era `/16`. Adicionamos os 5 bits que pegamos emprestado:

$$\text{Novo CIDR} = 16 + 5 = \mathbf{/21}$$

### Passo 3: Verificar a Necessidade de Hosts

Agora, garantimos que o novo `/21` atende ao segundo requisito: **pelo menos 1000 hosts úteis** em cada sub-rede.

1. Bits de Host Restantes:
    
    $$\text{Bits de Host} = 32 - \text{Novo CIDR} = 32 - 21 = \mathbf{11 \text{ bits}}$$
    
2. Total de Hosts Úteis:
    
    $$\text{Hosts Úteis} = 2^{\text{Bits de Host}} - 2$$
    
    $$\text{Hosts Úteis} = 2^{11} - 2 = 2048 - 2 = \mathbf{2046 \text{ hosts úteis}}$$
    

**Verificação:** 2046 hosts úteis é **maior** do que os 1000 hosts úteis exigidos. **O `/21` é a máscara ideal!**

### Passo 4: Definir a Máscara em Decimal

A máscara `/21` significa que os **21 primeiros bits** são '1's.

- Os dois primeiros octetos já são '1's (`255.255`).
    
- Faltam $21 - 16 = \mathbf{5 \text{ bits}}$ no **terceiro octeto**.
    

**Terceiro Octeto:** $\mathbf{11111}000$ (5 uns + 3 zeros)

- **Cálculo Decimal:** $128 + 64 + 32 + 16 + 8 = \mathbf{248}$
    

**Quarto Octeto:** $\mathbf{00000000}$ (8 zeros)

- **Cálculo Decimal:** $\mathbf{0}$
    

---

## ✅ A Resposta (Máscara Definida)

Para atender aos requisitos de **20 sub-redes** (resultando em 32 sub-redes disponíveis) e **1000 hosts úteis** (resultando em 2046 hosts úteis por sub-rede), a máscara ideal é:

|**Tipo**|**Máscara Definida**|
|---|---|
|**CIDR**|$\mathbf{/21}$|
|**Decimal**|$\mathbf{255.255.248.0}$|

Observe que a sub-rede agora está "quebrando" o **terceiro octeto** (o $\mathbf{248}$), o que é comum em redes de Classe B sub-redes.