Uma sub-rede (ou **subnet**) é uma subdivisão de uma rede IP (Internet Protocol). Pense na sua casa como uma rede. A sub-rede seria como cada cômodo da sua casa. Ao dividir uma rede grande em sub-redes menores, você consegue gerenciar o tráfego de dados de forma mais eficiente.

Essa divisão é feita usando a **máscara de sub-rede**. Ela diz a um roteador quais endereços IP pertencem à mesma sub-rede e quais não. É como um mapa que orienta o tráfador a enviar os pacotes de dados.

O objetivo principal das sub-redes é **reduzir o tráfego da rede** e **aumentar a segurança**. Ao dividir a rede em sub-redes, o tráfego destinado a um grupo de dispositivos não precisa passar por todos os outros, tornando a comunicação mais rápida e segura.

---

### Endereços IP e Faixas de IP

Um **endereço IP** é como o número de telefone de um dispositivo conectado à internet (computador, celular, impressora, etc.). Ele é um identificador único que permite que os dados sejam enviados e recebidos para o dispositivo correto.

Existem dois tipos principais de endereços IP:

- **IPv4**: É o formato mais antigo e comum. Ele é composto por quatro conjuntos de números, de 0 a 255, separados por pontos (por exemplo, `192.168.1.1`).
    
- **IPv6**: É o formato mais novo, criado para resolver a escassez de endereços IPv4. Ele é muito maior e mais complexo (por exemplo, `2001:0db8:85a3:0000:0000:8a2e:0370:7334`).
    

A usabilidade deles se diferencia entre os seguintes tipos:

- **IP público**: É o endereço IP que identifica o seu roteador para a internet. É através dele que você acessa sites e serviços online.
    
- **IP privado**: É o endereço IP que identifica dispositivos na sua rede interna (sua casa ou escritório). Esses endereços não são acessíveis diretamente da internet. Por isso, a maioria das redes domésticas usa a faixa de IP privado.
    

As faixas de IP são classificações para a usabilidade de cada tipo de rede. Veja os três tipos principais de faixas privadas, ou seja, as que podem ser usadas em redes internas:

1. **Classe A**: `10.0.0.0` até `10.255.255.255`
    
    - **Usabilidade:** Usada para redes muito grandes, como as de grandes empresas ou corporações, que precisam de um número massivo de endereços IP.
        
2. **Classe B**: `172.16.0.0` até `172.31.255.255`
    
    - **Usabilidade:** Usada para redes de médio a grande porte. É comum em universidades e empresas com um número significativo de dispositivos.
        
3. **Classe C**: `192.168.0.0` até `192.168.255.255`
    
    - **Usabilidade:** É a mais comum para **redes domésticas e pequenas empresas**. A maioria dos roteadores de internet que você usa em casa vem configurada para esta faixa de IP.
        


Esta imagem explica as três classes principais de endereços IPv4 e como as máscaras de sub-rede são usadas para definir o tamanho da rede e o número de dispositivos (hosts) que ela pode acomodar.

- **O Conceito:** Cada endereço IP é dividido em duas partes: a parte da **rede** (Network) e a parte do **host** (Host). A **máscara de sub-rede** é o que determina a divisão entre essas duas partes, dizendo ao roteador quais bits pertencem à rede e quais pertencem ao host. Onde a máscara tem `255`, a parte do endereço IP é para a rede. Onde tem `0`, a parte é para os hosts.
    
- **Classe A:**
    
    - **Máscara de Sub-rede:** `255.0.0.0`
        
    - **Divisão:** Usa 8 bits para a rede e 24 bits para os hosts.
        
    - **Usabilidade:** Ideal para redes gigantescas, como as de grandes corporações, pois permite um número enorme de hosts por rede (mais de 16 milhões).
        
- **Classe B:**
    
    - **Máscara de Sub-rede:** `255.255.0.0`
        
    - **Divisão:** Usa 16 bits para a rede e 16 bits para os hosts.
        
    - **Usabilidade:** Usada para redes de porte médio a grande, como as de universidades, pois oferece um bom equilíbrio entre o número de redes e o número de hosts.
        
- **Classe C:**
    
    - **Máscara de Sub-rede:** `255.255.255.0`
        
    - **Divisão:** Usa 24 bits para a rede e apenas 8 bits para os hosts.
        
    - **Usabilidade:** É a mais comum para redes pequenas, como as domésticas e de escritórios, pois o número de hosts por rede é limitado (256), mas o número de redes disponíveis é gigantesco.

___
![[subnet-img-01.png]]
Esta imagem explica as três classes principais de endereços IPv4 e como as máscaras de sub-rede são usadas para definir o tamanho da rede e o número de dispositivos (hosts) que ela pode acomodar.

- **O Conceito:** Cada endereço IP é dividido em duas partes: a parte da **rede** (Network) e a parte do **host** (Host). A **máscara de sub-rede** é o que determina a divisão entre essas duas partes, dizendo ao roteador quais bits pertencem à rede e quais pertencem ao host. Onde a máscara tem `255`, a parte do endereço IP é para a rede. Onde tem `0`, a parte é para os hosts.
    
- **Classe A:**
    
    - **Máscara de Sub-rede:** `255.0.0.0`
        
    - **Divisão:** Usa 8 bits para a rede e 24 bits para os hosts.
        
    - **Usabilidade:** Ideal para redes gigantescas, como as de grandes corporações, pois permite um número enorme de hosts por rede (mais de 16 milhões).
        
- **Classe B:**
    
    - **Máscara de Sub-rede:** `255.255.0.0`
        
    - **Divisão:** Usa 16 bits para a rede e 16 bits para os hosts.
        
    - **Usabilidade:** Usada para redes de porte médio a grande, como as de universidades, pois oferece um bom equilíbrio entre o número de redes e o número de hosts.
        
- **Classe C:**
    
    - **Máscara de Sub-rede:** `255.255.255.0`
        
    - **Divisão:** Usa 24 bits para a rede e apenas 8 bits para os hosts.
        
    - **Usabilidade:** É a mais comum para redes pequenas, como as domésticas e de escritórios, pois o número de hosts por rede é limitado (256), mas o número de redes disponíveis é gigantesco.


___

![[subnet-img-02.png]]
Esta imagem demonstra o conceito de **subnetting**, que é a prática de dividir uma grande rede IP em sub-redes menores.

- **A Rede Original:** A rede principal é a `200.1.1.0/24`. O **/24** significa que os primeiros 24 bits (ou os três primeiros números, `200.1.1.`) são fixos e identificam a rede, enquanto os 8 bits restantes (o último número, de `0` a `255`) podem ser usados para endereços de dispositivos. Isso resulta em um total de 256 endereços de IP disponíveis na rede original (`200.1.1.0` a `200.1.1.255`).
    
- **As Sub-redes (Subnets):** A imagem mostra como essa rede de 256 endereços foi "cortada" (com a metáfora da serra elétrica) em sub-redes menores para atender a diferentes locais (lojas). Cada sub-rede tem um propósito específico:
    
    - **Subnet 1:** Usada para a loja de Nova York (NY Store) e tem 32 endereços de IP.
        
    - **Subnet 2:** Usada para conectar a Roteador 1 e a Roteador 2, e tem 4 endereços de IP.
        
    - **Subnet 3:** Também usada para a interconexão de roteadores (Roteador 2 e Roteador 4), com 4 endereços.
        
    - **Subnet 4:** Usada para a loja de Los Angeles (LA Store), com 16 endereços.
        
    - **Subnet 5:** Usada para a loja de Chicago (CHI Store), com 16 endereços.
        
    - **Endereços Restantes:** Após a divisão, ainda restam 184 endereços de IP que podem ser usados para criar novas sub-redes no futuro.
        
- **A Rota (Roteadores):** A parte de baixo da imagem mostra a arquitetura física. Os roteadores (`Router 1` a `Router 4`) são os dispositivos que conectam essas sub-redes. Eles direcionam o tráfego de dados de uma sub-rede para outra, garantindo que os dados de uma loja cheguem à outra, se necessário.