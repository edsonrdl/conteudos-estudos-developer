O que as difere é a **divisão fixa** do endereço IP entre a parte da **Rede** e a parte do **Host** (dispositivo). Essa divisão é determinada pelo **primeiro octeto** (o primeiro número) do endereço IP e define a **Máscara de Sub-rede Padrão** de cada classe.

### Resumo das Diferenças

|Característica|Classe A|Classe B|Classe C|
|---|---|---|---|
|**Faixa do 1º Octeto**|**1 a 126**|**128 a 191**|**192 a 223**|
|**Máscara Padrão**|**255.0.0.0**|**255.255.0.0**|**255.255.255.0**|
|**Divisão (Rede.Host)**|**Rede** . Host . Host . Host|**Rede . Rede** . Host . Host|**Rede . Rede . Rede** . Host|
|**Hosts/Rede (Máximo)**|∼16,7 milhões|∼65 mil|**254**|
|**Usabilidade**|Redes **muito grandes** (grandes corporações).|Redes de **médio/grande porte** (universidades).|Redes **pequenas** (domésticas/pequenas empresas).|

Exportar para as Planilhas

---

### Explicação Detalhada

#### 1. Divisão Rede vs. Host

A principal diferença é a quantidade de bits (e, portanto, octetos) que cada classe reserva para identificar a **Rede** e o **Host**.

- **Classe A:** Dedica apenas o **primeiro** octeto (8 bits) à rede e os três octetos restantes (24 bits) para os hosts.
    
    - **Resultado:** Permite poucas redes (126 redes no total), mas cada uma delas pode ter um número gigantesco de dispositivos (hosts).
        
    - _Exemplo: `10.x.x.x` é uma rede, e você pode ter milhões de máquinas nela._
        
- **Classe B:** Dedica os **dois primeiros** octetos (16 bits) à rede e os dois octetos restantes (16 bits) para os hosts.
    
    - **Resultado:** Um equilíbrio. Permite mais redes do que a Classe A, mas com um número limitado de hosts por rede.
        
- **Classe C:** Dedica os **três primeiros** octetos (24 bits) à rede e apenas o último octeto (8 bits) para os hosts.
    
    - **Resultado:** Permite um número massivo de redes, mas cada rede é minúscula, suportando no máximo **254 dispositivos** (Hosts).
        
    - _Exemplo: A sua rede doméstica, `192.168.1.x`, é a mais comum dessa classe._
        

#### 2. Máscara de Sub-rede Padrão

A máscara padrão acompanha a regra de divisão da classe:

- Onde está escrito **Rede**, a máscara é 255.
    
- Onde está escrito **Host**, a máscara é 0.
    

|Classe|Divisão|Máscara Padrão|
|---|---|---|
|A|Rede . Host . Host . Host|255 . 0 . 0 . 0|
|B|Rede . Rede . Host . Host|255 . 255 . 0 . 0|
|C|Rede . Rede . Rede . Host|255 . 255 . 255 . 0|

Exportar para as Planilhas

---

### A Observação Importante: CIDR

É importante notar que o sistema de classes (**Classful**) é o modelo **original** do IPv4.

No entanto, ele causava um enorme **desperdício de endereços**. Por exemplo, se uma empresa de médio porte precisasse de 500 hosts, ela teria que usar uma rede Classe B (que permite 65.534 hosts), desperdiçando milhares de endereços.

Para resolver isso, a internet moderna usa um sistema mais flexível chamado **CIDR (Classless Inter-Domain Routing)**. O CIDR ignora as regras fixas de Classe A, B e C e usa a notação de barra (`/24`, `/25`, etc.) para definir o tamanho da rede de forma **precisa**, permitindo a criação de sub-redes de qualquer tamanho.

Ou seja: você ainda pode ver o conceito de classes, mas a regra de **divisão** e a **máscara** de rede são definidas pelo CIDR.