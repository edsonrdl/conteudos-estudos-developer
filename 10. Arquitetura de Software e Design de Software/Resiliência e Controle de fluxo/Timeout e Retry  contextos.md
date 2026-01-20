### 1. 🔍 Requisição (Request)

- **O que é:** É um **pedido** feito por um programa (o **cliente**) para outro programa ou serviço (o **servidor**), solicitando alguma ação ou dado.
    
- **Exemplo:**
    
    - Quando você digita um endereço em seu navegador e aperta Enter, o navegador faz uma **requisição HTTP** para o servidor daquele site.
        
    - Um aplicativo no seu celular pode fazer uma requisição a uma API (Interface de Programação de Aplicativos) para buscar informações do seu perfil.
        
- **Em resumo:** É o ato de **solicitar** algo.
    

---

### 2. ⏳ Timeout (Tempo Limite)

- **O que é:** É o **tempo máximo** que o programa cliente está disposto a esperar por uma resposta após enviar uma requisição.
    
- **Para que serve:** Garante que o cliente não fique esperando indefinidamente por um serviço que está lento ou indisponível. Se a resposta não chegar dentro desse tempo limite, a requisição é cancelada e o cliente recebe um erro de _timeout_.
    
- **Exemplo:** Você configura que uma requisição a um banco de dados deve levar no máximo 5 segundos. Se passar de 5 segundos, o _timeout_ é atingido e o programa desiste de esperar, registrando um erro.
    
- **Em resumo:** É o **limite de tempo** para uma resposta.
    

---

### 3. 🔁 Retry (Tentativa/Nova Tentativa)

- **O que é:** É a **estratégia** de **repetir** uma requisição que falhou, geralmente após um curto período de espera (atraso).
    
- **Quando usar:** É útil para lidar com **erros temporários** ou **transientes**, como:
    
    - Instabilidade momentânea na rede.
        
    - Um servidor que está temporariamente sobrecarregado (e pode se recuperar em segundos).
        
    - _Timeouts_ causados por picos de tráfego.
        
- **Mecanismo:**
    
    - A requisição falha (por _timeout_, por exemplo).
        
    - O programa espera um pouco (o atraso pode aumentar a cada tentativa, o que é chamado de _backoff exponencial_ para não sobrecarregar o servidor).
        
    - O programa faz uma nova tentativa.
        
    - Isso se repete até um número **máximo de tentativas** ser atingido ou a requisição ser bem-sucedida.
        
- **Em resumo:** É a **lógica de repetição** para superar falhas temporárias.
    

---

### Resumo da Conexão

Eles funcionam juntos na construção de sistemas **resilientes**:

1. Um programa faz uma **Requisição** a um serviço.
    
2. Um **Timeout** é definido para que o programa não espere para sempre.
    
3. Se a Requisição falha ou atinge o Timeout, a estratégia de **Retry** entra em ação, tentando novamente um número limitado de vezes para ver se o problema era temporário.

### 💡 Padrões Essenciais para um Bom Cenário

O segredo não é apenas usar `Timeout` e `Retry`, mas combiná-los com boas práticas:

#### 1. ⚙️ Timeout: Definir o Limite Certo

O _timeout_ deve ser configurado em dois níveis:

- **Timeout de Rede (Conexão e Leitura):** O tempo que leva para estabelecer a conexão e o tempo para ler o primeiro byte da resposta.
    
- **Timeout Total da Operação:** O tempo total que a aplicação cliente espera por uma resposta completa.
    

|**Configuração**|**Descrição**|**Boa Prática**|
|---|---|---|
|**Valor do Timeout**|Deve ser alto o suficiente para permitir que a maioria das requisições legítimas e lentas terminem, mas baixo o suficiente para não prender recursos (threads) em caso de falha.|**Análise de Latência:** Verifique a latência média (o "tempo normal") e defina o _timeout_ como 2 a 3 vezes esse valor médio.|
|**Impacto do Timeout**|Se o _timeout_ for muito alto, ele pode causar **falhas em cascata**, pois a aplicação cliente fica presa esperando, esgotando seus próprios recursos.|Um _timeout_ bem ajustado é a **primeira linha de defesa** contra serviços lentos.|

#### 2. 🔁 Retry: A Estratégia Inteligente

O `Retry` deve ser implementado com inteligência para evitar o temido **"Retry Storm"** (tempestade de novas tentativas, onde todos os clientes tentam novamente ao mesmo tempo, sobrecarregando ainda mais o serviço alvo).

|**Estratégia**|**Como Funciona**|**Por Que é Bom**|
|---|---|---|
|**Backoff Exponencial**|O tempo de espera entre as tentativas aumenta exponencialmente: 1s, 2s, 4s, 8s, etc.|**Evita Saturação:** Dá tempo para que o serviço alvo se recupere.|
|**Jitter (Atraso Aleatório)**|Adiciona uma pequena aleatoriedade ao tempo de espera (ex: 1s + 0.3s aleatórios).|**Evita o _Retry Storm_**: Garante que múltiplas instâncias cliente não tentem novamente no **exato mesmo momento**.|
|**Limite de Tentativas**|Defina um número máximo de tentativas (ex: 3 a 5).|**Protege o Usuário:** Impede que a aplicação tente infinitamente, garantindo que o erro chegue ao usuário ou ao log após um tempo razoável.|

---

### 🚀 O Cenário Mais Robusto (O Trio Resiliente)

Para ter um sistema que realmente pondera a resiliência, você deve introduzir um terceiro "cara": o **Circuit Breaker (Disjuntor)**.

|**Padrão**|**Função**|**Como Pondera os Outros Dois**|
|---|---|---|
|**Timeout**|Define um limite de tempo.|Uma requisição que atinge o _Timeout_ é contada como uma **falha** para o _Circuit Breaker_.|
|**Retry**|Tenta novamente após falhas temporárias.|O _Retry_ funciona enquanto o _Circuit Breaker_ estiver **Fechado** (tudo normal).|
|**Circuit Breaker**|**Monitora** a taxa de falhas e, se ela for alta (ex: 50% das últimas 10 requisições), o "circuito abre" e bloqueia novas requisições.|**Previne Falhas em Cascata:** Se o serviço de destino estiver fora do ar ou muito lento, o _Circuit Breaker_ **impede** o _Retry_ (e novas requisições) por um tempo (ex: 60 segundos), retornando erro imediatamente. Isso salva recursos no cliente e dá tempo para o servidor se recuperar.|

#### Exemplo de Fluxo (O Cenário Completo)

1. **Requisição é Enviada:** O Cliente A chama o Serviço B.
    
2. **Verificação do Circuit Breaker:** O Disjuntor verifica se está **Fechado** (status normal).
    
3. **Execução com Timeout:** A chamada para o Serviço B tem um _Timeout_ de 5 segundos.
    
4. **Falha Transitória (Exemplo):** A requisição falha com erro de rede ou _Timeout_.
    
5. **Ação do Retry:** O sistema entra no loop de `Retry` (ex: 3 tentativas com _Backoff Exponencial_ e _Jitter_).
    
6. **Falha Crônica:** Após 3 tentativas, o serviço continua inoperante.
    
7. **Ação do Circuit Breaker:** O Disjuntor registra a falha. Se o número de falhas consecutivas ultrapassar um limite, o Disjuntor **Abre**.
    
8. **Proteção Ativada:** Para as próximas requisições, o Disjuntor **retorna um erro imediatamente** sem sequer tentar o _Timeout_ ou o _Retry_, protegendo ambos os sistemas.
    

Dessa forma, você cria um sistema que é **insistente** com problemas pequenos e **desistente** (por um tempo) com problemas grandes.****