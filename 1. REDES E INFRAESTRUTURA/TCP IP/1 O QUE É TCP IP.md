**TCP/IP** (Transmission Control Protocol / Internet Protocol) é o **conjunto de protocolos que permite a comunicação entre dispositivos na internet e redes locais**. Ele define **como os dados são enviados, recebidos e organizados** entre dispositivos conectados.

➡️ Pense no **TCP/IP como o sistema postal da internet**:

- O **IP (Internet Protocol)** é o **endereço do destinatário** no envelope (define para onde os dados vão).
- O **TCP (Transmission Control Protocol)** é o **caminho e a garantia de entrega** (quebra os dados em pacotes e garante que cheguem na ordem correta).

---
## **🔹 Como Funciona o TCP/IP?**

📌 Quando você acessa um site, faz download de um arquivo ou usa um app online, o TCP/IP age da seguinte forma:

1️⃣ **Divisão dos Dados (TCP)**

- O TCP divide a informação em **pequenos pacotes de dados** antes de enviá-los pela rede.

2️⃣ **Endereçamento e Roteamento (IP)**

- Cada pacote recebe um **endereço IP de destino** (como `192.168.1.1` ou `8.8.8.8`).

3️⃣ **Envio dos Pacotes**

- Os pacotes viajam por **roteadores e servidores** até chegarem ao destino.

4️⃣ **Verificação e Reorganização (TCP)**

- O TCP no computador de destino verifica se **todos os pacotes chegaram corretamente e os coloca na ordem certa**.

---

## **🔹 Diferença Entre TCP e IP**

|**Protocolo**|**Função**|
|---|---|
|**IP (Internet Protocol)**|Identifica e direciona os pacotes de dados para o destino correto (usa endereços como `192.168.1.1`).|
|**TCP (Transmission Control Protocol)**|Garante que os pacotes chegam completos, na ordem correta e sem erros.|

📌 **Exemplo:**

- Quando você acessa `https://www.google.com`, seu navegador envia um **pedido TCP/IP para o IP do Google**.
- O **IP roteia** a requisição até o servidor certo.
- O **TCP divide os dados**, garante que chegam na ordem correta e sem erros.

---

## **🔹 Camadas do Modelo TCP/IP**

O TCP/IP é baseado em **4 camadas**, que organizam como os dados trafegam na rede:

|**Camada**|**Responsabilidade**|**Exemplos**|
|---|---|---|
|**Aplicação**|Permite que aplicativos usem a rede|HTTP, HTTPS, FTP, DNS, SMTP|
|**Transporte**|Garante que os dados cheguem corretamente|TCP, UDP|
|**Internet**|Define como os pacotes são roteados|IP, ICMP|
|**Rede (Acesso)**|Controla o envio físico dos dados|Ethernet, Wi-Fi|

---

## **🔹 TCP vs UDP**

Além do TCP, há outro protocolo de transporte chamado **UDP (User Datagram Protocol)**.

|**Protocolo**|**Confiabilidade**|**Velocidade**|**Uso**|
|---|---|---|---|
|**TCP**|Confiável (corrige erros e garante entrega)|Mais lento|Web, e-mails, downloads|
|**UDP**|Rápido (não verifica erros)|Mais rápido|Streaming, jogos online, VoIP|

📌 **Exemplo:**

- **Um e-mail usa TCP** (precisa garantir que todas as letras da mensagem cheguem corretamente).
- **Um jogo online usa UDP** (se um pequeno pacote de dados se perder, o jogo continua sem atrasos).

---
### Conceitos Básicos

- **Endereço IP (Internet Protocol)**: identificador único de um dispositivo em uma rede.
- **IPv4**: formato clássico de 32 bits, dividido em 4 octetos (ex: 192.168.0.1).
- **IPv6**: formato mais recente de 128 bits (ex: 2001:0db8:85a3::7334).

> Analogia: imagine ruas (IPs) e bairros (sub-redes). Ruas internas de um condomínio são faixas privadas; ruas de rodovias federais são IPs públicos.

---

### Faixas IPv4 e Seus Usos

###  Faixas Privadas

|Faixa CIDR|Intervalo|Uso|
|---|---|---|
|10.0.0.0/8|10.0.0.0 – 10.255.255.255|Grandes LANs, corporações|
|172.16.0.0/12|172.16.0.0 – 172.31.255.255|Sub-redes médias|
|192.168.0.0/16|192.168.0.0 – 192.168.255.255|Redes domésticas|

###  Faixas Públicas

- **Tudo que não estiver nas listas privadas ou especiais**. Exemplo: 8.8.8.8 (DNS público do Google).
- **Atribuída por provedores de Internet**; roteada na Internet global.

###  Faixas Especiais e Reservadas

|Faixa CIDR|Descrição|
|---|---|
|127.0.0.0/8|Loopback (testes locais)|
|169.254.0.0/16|APIPA/Link‑Local (sem DHCP)|
|224.0.0.0/4|Multicast|
|240.0.0.0/4|Reservadas (futuro/experimental)|
|0.0.0.0/8|Rede não especificada (uso interno)|

---

###  Como Identificar Cada Tipo

1. **Verificar o primeiro octeto**:
    - `10.x.x.x` → Privado
    - `172.16.x.x` a `172.31.x.x` → Privado
    - `192.168.x.x` → Privado
2. **Checar 127.x.x.x** → Loopback
3. **Checar 169.254.x.x** → Link‑Local
4. **Se não pertencer a nenhum acima** → Público

> Dica de Memorização: lembrar as "ruas do condomínio": 10 (dez), 172.16–31 (dezessete a trinta e um), 192.168 (casa). Tudo fora disso é cidade (internet pública).

---

### Sub-redes e Máscaras

###  Cálculo de Intervalo

- **Máscara** em notação decimal (ex: 255.255.255.0) ou CIDR (/24).
- **Hosts** = 2^(32 - prefixo) – 2 (rede + broadcast).

###  Endereço de Rede e Broadcast

- **Rede**: aplicar máscara ao IP pelo AND bit a bit.
- **Broadcast**: inverter máscara e somar ao endereço de rede.

**Exemplo**: IP 192.168.1.10/28

- Rede: 192.168.1.0
- Broadcast: 192.168.1.15
- Hosts: 14 endereços úteis (1–14)

---

###  Ferramentas e Comandos Práticos

- **Windows**: `ipconfig /all`
- **Linux/macOS**: `ip addr show` ou `ifconfig`
- **Consulta IP público**: `curl ifconfig.me` ou acessar sites como [WhatIsMyIP.com](http://WhatIsMyIP.com)
- **Calculadoras de sub-rede**: sites como [subnet-calculator.com](http://subnet-calculator.com) ou ferramentas CLI (`sipcalc`, `ipcalc`).

---

###  IPv6: Panorama Rápido

- **Formato**: 128 bits, hexadecimais, abreviação de zeros (::).
- **Faixas comuns**:
    - `fc00::/7` → Privado (ULA)
    - `fe80::/10` → Link‑Local
    - `2001:db8::/32` → Documentação

---

###  Quiz de Fixação

1. IP `192.168.0.50` → ________
2. IP `8.8.4.4` → ________
3. IP `169.254.10.5` → ________
4. IP `127.0.0.1` → ________

**Respostas**: 1) Privado, 2) Público, 3) Link‑Local, 4) Loopback

Mais sobre [[1. REDES E INFRAESTRUTURA/TCP IP/Protocolo IP (Internet Protocol)/Sub-redes (Subnets).md | Sub-redes(Subnetes)]]

---