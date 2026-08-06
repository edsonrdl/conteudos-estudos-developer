![[img-teorema-cap.png]]

## 1. Visão didática do Teorema CAP

### 1.1. Contexto: por que CAP existe?

O Teorema CAP fala sobre sistemas distribuídos, isto é, sistemas onde seus dados e processamento estão espalhados em várias máquinas (nós) que se comunicam por rede.

Esses sistemas existem por motivos bem práticos:

- Escalar horizontalmente (mais máquinas para aguentar mais carga).
    
- Melhorar disponibilidade (se um nó cai, outros continuam).
    
- Reduzir latência (nó mais próximo do usuário).
    
- Aumentar resiliência (tolerar falhas de hardware/rede).
    

Só que ao distribuir, você ganha problemas:

- Rede pode falhar parcial ou totalmente (perda de pacotes, timeout, partição).
    
- Máquinas podem cair, voltar, ficar lentas, duplicar mensagens, etc.
    
- Mensagens podem chegar em ordem diferente do envio.
    

O Teorema CAP é justamente uma forma de enunciar uma **limitação fundamental**: diante de certos tipos de falhas (partições de rede), você é obrigado a abrir mão de alguma coisa.

---

### 1.2. O que significa cada letra: C, A, P

No contexto do CAP, essas palavras têm significados específicos (que não são exatamente os mesmos da literatura de banco relacional, por exemplo):

#### 1.2.1. C – Consistência (no sentido CAP, não ACID)

No CAP, **Consistência** significa algo muito próximo de **linearizabilidade**:

> Em qualquer leitura, o cliente sempre enxerga o dado mais recente que foi confirmado com sucesso em qualquer nó do sistema, como se existisse uma única cópia “global” do dado e todas as operações fossem aplicadas em série.

Em outros termos:

- Todos os nós veem as mesmas atualizações na mesma ordem.
    
- Assim que você recebe resposta de sucesso em uma escrita, qualquer leitura em qualquer nó (que responda) deve ver aquela atualização.
    
- Parece que existe um único banco central, mesmo que na prática o dado esteja replicado.
    

Isso é uma **consistência forte**.

Não confundir com:

- Consistência de **integridade referencial** (chaves estrangeiras, constraints).
    
- “Consistência eventual”, que já é uma forma mais fraca.
    

CAP está falando dessa visão forte: “se gravei, qualquer leitura logo depois deve ver o valor gravado”.

#### 1.2.2. A – Disponibilidade

No CAP, **Disponibilidade** significa:

> Toda requisição a um nó não falho recebe alguma resposta (de sucesso ou erro de aplicação), em tempo finito.

Pontos importantes:

- “Tempo finito” → o sistema não pode ficar esperando eternamente; ele precisa decidir.
    
- “Alguma resposta” → não pode simplesmente ignorar o cliente; tem que responder “ok”, ou “falha de negócio”, etc. (mas não timeout eterno).
    
- “Nó não falho” → se o nó está de pé (processo vivo), ele precisa responder as requisições.
    

Note que, no CAP, um sistema que responde “não sei” (ou “não posso atender agora por causa da partição”) ainda pode ser considerado **indisponível**, se ele não responde ou fica bloqueado demais. Disponibilidade implica que o nó tente servir as requisições, em vez de travar.

#### 1.2.3. P – Tolerância a Partições

**Partição de rede** é quando o cluster de nós é “cortado” em grupos que não conseguem mais se comunicar entre si, mas **cada grupo interno continua funcionando**.

Imagine:

- Cluster com 3 nós: N1, N2, N3.
    
- De repente, N1 não consegue mais falar com N2 e N3, mas N2 e N3 conseguem falar entre si.
    
- Você tem dois “sub-clusters” desconectados.
    

**Tolerância a Partições** significa:

> O sistema continua funcionando mesmo quando há perda de comunicação entre partes do cluster, ou seja, ele assume que partições de rede podem ocorrer e lida com isso.

Em um sistema realmente distribuído, **não tolerar partições** basicamente significa: “se a rede falhar de forma grave, eu aceito que o sistema como um todo pode parar ou assumir falha global”.

---

### 1.3. Enunciado informal do Teorema CAP

Versão didática clássica:

> Em um sistema distribuído, na presença de uma partição de rede, você é obrigado a escolher entre:
> 
> - ou manter **Consistência** (C) e abrir mão de **Disponibilidade** (A),
>     
> - ou manter **Disponibilidade** (A) e abrir mão de **Consistência forte** (C).
>     

Em frase de livro:

> Não é possível construir um sistema distribuído que, ao mesmo tempo, garanta Consistência forte, Alta Disponibilidade e Tolerância a Partições de rede.

Ou ainda, na forma popular (mas simplificada):

> Você só pode ter dois dos três: C, A ou P.  
> (Essa frase é um **abuso didático**, mas ajuda a memorizar.)

O ponto essencial:

- **Enquanto não há partição**, você pode “parecer” ter C e A juntos.
    
- **Quando a partição acontece**, a impossibilidade aparece: você precisa sacrificar algo.
    

---

### 1.4. Intuição com exemplos

#### Cenário 1: Sem partição

Você tem dois nós, N1 e N2, replicando dados.

- Rede está perfeita.
    
- Você consegue coordenar escritas:
    
    - Escreve em N1, ele replica para N2, só responde “OK” quando N2 confirmou.
        
- Leituras podem ser feitas de qualquer nó, e sempre recebem o último valor.
    

Aqui, parece que você tem:

- C: porque você só confirma uma escrita quando a replicação foi feita, garantindo visão consistente.
    
- A: porque todos os nós respondem.
    
- P: irrelevante, porque não há partição.
    

Consenso: sem falhas de rede, é relativamente fácil ter o “melhor dos mundos”.

#### Cenário 2: Com partição

Agora, imagine que a rede quebra:

- N1 fica isolado de N2.
    
- Usuários continuam mandando requisições para os dois lados.
    

Você quer manter:

- P: o sistema deve suportar partição (não ligar o “modo pânico” global).
    
- Agora você precisa escolher entre:
    
    - C (Consistência forte)
        
    - A (Disponibilidade)
        

##### Opção 1: Priorizar C (sistemas CP)

Você quer garantir consistência forte. Então:

- Se N1 não consegue falar com N2, e a sua regra é:
    
    - “Só aceito uma escrita se conseguir replicar para a maioria dos nós.”
        
- Diante da partição, um dos lados não tem quórum suficiente.
    
- Esse lado, então, **recusa** escritas (e talvez leituras), retornando erro ou travando.
    

O que isso causa?

- Consistência é mantida: você não corre o risco de escrever valores diferentes em lados isolados.
    
- Mas disponibilidade cai: alguns clientes, falando com o lado “minoritário” da partição, não vão conseguir escrever (e às vezes nem ler).
    

Esses sistemas são chamados de **CP** (Consistent + Partition tolerant).

Exemplos típicos (intuitivos, sem entrar em detalhe):

- Zookeeper, etcd, Consul (armazenam metadados e usam consenso tipo Raft/Paxos).
    
- Bancos com replicação síncrona por consenso.
    

##### Opção 2: Priorizar A (sistemas AP)

Agora você decide:

- “Mesmo com partição, eu quero que cada nó continue respondendo a leituras e escritas.”
    

Nesse caso:

- N1 e N2 continuam aceitando requisições, mesmo sem conversar entre si.
    
- Você pode escrever X em N1 e Y em N2 para a mesma chave.
    

O que acontece?

- O sistema continua disponível: todo mundo recebe resposta.
    
- Mas **Consistência forte se perde**: diferentes nós podem ter valores diferentes para o mesmo dado até que a partição seja sanada.
    
- Depois, quando a rede volta, você precisa ter uma **estratégia de resolução de conflitos**:
    
    - “Last write wins” (timestamp).
        
    - Versões vetoriais.
        
    - Merge customizado.
        

Esses sistemas são chamados de **AP** (Available + Partition tolerant).

Exemplos intuitivos:

- Família Dynamo (Amazon Dynamo, Cassandra, Riak).
    
- Sistemas de cache distribuídos que aceitam “eventual consistency”.
    

---

### 1.5. Mito comum: “é só escolher dois”

A frase “escolha dois dos três” é muito repetida, mas:

- Em sistemas **realmente distribuídos**, P (Tolerância a Partições) não é opcional:
    
    - A rede vai falhar.
        
    - Você não tem controle total sobre isso.
        
- Logo, na prática, o trade-off real é: **CP x AP**.
    

Ou seja:

- CP: você aceita ficar indisponível em partes para manter consistência forte.
    
- AP: você aceita inconsistência temporária para manter disponibilidade alta.
    

CA (Consistente e Disponível, ignorando P) só existe de fato em:

- Sistema de nó único.
    
- Cluster estreitamente acoplado que, quando tem falha de rede séria, considera-se como “sistema morto” (não tenta operar em partição).
    

---

### 1.6. Ligando CAP a exemplos do mundo real

- Bancos tradicionais com um único servidor central: **CA** enquanto não há partição relevante (na prática, se o servidor cai, acabou tudo).
    
- Zookeeper / etcd: **CP** – preferem recusar operação do que aceitar algo que possa violar consistência.
    
- Cassandra / DynamoDB modo eventual: **AP** – aceitam operações mesmo com partições; consistência é eventual, com conflitos resolvidos depois.
    

---

## 2. Detalhamento avançado

Agora vamos mais a fundo: formalismo, nuances e implicações arquiteturais.

---

### 2.1. Origem formal do Teorema CAP

Historicamente:

- Em 2000, Eric Brewer apresentou uma conjectura (o “CAP theorem”) numa palestra.
    
- Em 2002, Seth Gilbert e Nancy Lynch produziram a prova formal do teorema, em termos de modelos de sistemas distribuídos.
    

Eles trabalharam em um modelo com:

- Sistema assíncrono (sem limites garantidos de tempo de entrega de mensagem).
    
- Possibilidade de falhas de comunicação (partições).
    
- Clientes fazendo operações de leitura/escrita em nós arbitrários.
    

A conclusão formal, simplificada:

> Em um sistema assíncrono, que pode sofrer partições, não é possível garantir simultaneamente:
> 
> 1. Consistência linearizável.
>     
> 2. Disponibilidade (resposta em tempo finito por qualquer nó não falho).
>     

Observações:

- “Linearizável” é um modelo de consistência forte que exige que todas as operações pareçam acontecer em uma única linha do tempo global respeitando a ordem real.
    
- Disponibilidade é definida de maneira bem rigorosa: nenhum nó não falho pode se recusar a responder indefinidamente.
    

A prova mostra que, sob partição, ou você:

- viola linearizabilidade (retornando dados possivelmente desatualizados),  
    ou
    
- viola disponibilidade (recusando responder enquanto não houver certeza).
    

---

### 2.2. CAP não fala de performance, latência ou throughput

Ponto importante:

- CAP não fala nada sobre:
    
    - Tempo médio de resposta.
        
    - Throughput.
        
    - Eficiência.
        
- CAP só considera:
    
    - Se o sistema eventualmente responde (disponibilidade).
        
    - Se a resposta é consistente de acordo com o modelo (linearizabilidade).
        
    - Se há partição ou não.
        

Mas, na prática, arquitetos precisam também equilibrar:

- Latência de replicação.
    
- Custos de rede.
    
- Topologias multi-região.
    

Daí surgem extensões como o **PACELC** (falarei abaixo).

---

### 2.3. Modelo de consistência em detalhes

No CAP, a “C” é consistência tipo **linearizável**:

- Toda operação parece ocorrer instantaneamente em algum ponto entre o envio e a resposta.
    
- Todas as threads/processos veem as operações na mesma ordem.
    

Na prática, sistemas relaxam isso:

- Consistência sequencial.
    
- Causal.
    
- Eventual.
    

Um sistema **AP** é tipicamente:

- **Consistente eventual**: se você parar de escrever e deixar o cluster estabilizar, eventualmente todos os nós convergem para o mesmo valor.
    
- **Durante partições**, você aceita divergência temporária para manter disponibilidade.
    

Um sistema **CP** tende a:

- Priorizar um modelo próximo de linearizabilidade ou, no mínimo, muito forte (quórum + bloqueio, etc.).
    
- Usar protocolos de consenso (Raft, Paxos) para garantir ordem global.
    

---

### 2.4. CAP x PACELC

CAP lida com o trade-off **quando há partição** (P). Mas o dia a dia também tem o cenário normal, **sem partição**. Foi proposto o modelo **PACELC**:

- Se houver Partição (P):
    
    - Escolha entre Availability (A) ou Consistency (C).
        
- Else (se não há partição, E):
    
    - Escolha entre Latency (L) ou Consistency (C).
        

Ou seja:

- Mesmo sem partição, há trade-offs:
    
    - Para garantir consistência forte entre várias réplicas, você normalmente:
        
        - Espera replicação para múltiplos nós.
            
        - Aumenta latência.
            
    - Se você quer baixa latência, talvez precise relaxar um pouco a consistência.
        

Exemplos PACELC:

- Dynamo / Cassandra: **PA/EL** – com partição, favorecem disponibilidade. Sem partição, favorecem baixa latência em detrimento de consistência forte.
    
- Sistemas tipo Spanner (Google): **PC/EC** – com partição, favorecem consistência; sem partição, ainda favorecem consistência mesmo custando mais latência (sincronizações via relógio atômico, etc.).
    

---

### 2.5. Implicações arquiteturais práticas

Quando você projeta sistemas distribuídos (principalmente no contexto que você já estuda: microsserviços, bancos distribuídos, mensageria, etc.), o CAP se manifesta em decisões concretas.

#### 2.5.1. Microsserviços e comunicação entre serviços

Imagine:

- Serviço A (Orders).
    
- Serviço B (Payments).
    
- Banco replicado, mensageria, etc.
    

Você precisa decidir:

- Se um serviço remoto está inacessível (partição lógica: vai dar timeout), o que você faz?
    
    - Bloqueia a requisição do cliente até voltar?  
        → **Consistência maior, disponibilidade menor** (tendência CP).
        
    - Aceita a requisição mesmo sem confirmação, manda para uma fila para processamento posterior?  
        → **Disponibilidade maior, consistência menor (eventual)** (tendência AP).
        

Esses padrões:

- **Sagas**, **eventual consistency**, **outbox pattern**, **fila de compensação** → estilos de arquitetura que optam por AP/PA.
    
- **Transações distribuídas 2PC (Two-Phase Commit)** → empurram para CP, mas com custo de latência e risco de bloqueios.
    

#### 2.5.2. Caches e repositórios

Cache distribuído (ex: Redis cluster, cache HTTP):

- Se o cache de um nó fica “atrasado” em relação ao banco, você pode:
    
    - Bloquear até sincronizar (difícil, geralmente não é feito).
        
    - Aceitar devolver dado possivelmente desatualizado (consistência eventual).
        

Na prática, caches são pensados quase sempre como **AP e eventualmente consistentes**.

#### 2.5.3. Bancos de dados distribuídos

Projetando um banco:

- Se você exige que cada escrita seja replicada e confirmada em maioria de nós antes de responder, você está em CP.
    
- Se você permite gravar local e replicar depois, aceitando divergência temporária, você está em AP.
    

Ferramentas de configuração típicas:

- N (número de réplicas).
    
- W (quórum de escrita: quantas réplicas precisam confirmar a escrita).
    
- R (quórum de leitura: quantas réplicas você lê para responder).
    

Regras de quórum estilo Dynamo:

- Se R + W > N, você tende a ter consistência mais forte (no limite, linearizável com outras condições).
    
- Se R e W forem baixos, disponibilidade aumenta, mas consistência se enfraquece.
    

---

### 2.6. “CAP awareness”: como raciocinar em entrevistas e projetos

Uma forma madura de discutir CAP é:

1. **Reconhecer que partições acontecem**:
    
    - Multi-região, internet pública, cloud: partições lógicas são inevitáveis.
        
    - Logo, você está sempre, na prática, num mundo CAP onde P precisa ser tratado.
        
2. **Decidir, caso a caso, CP ou AP**:
    
    - Perguntar: “Neste contexto de negócio, o que é mais crítico?”
        
        - “Eu posso mostrar para o usuário um saldo ligeiramente desatualizado?”
            
            - Se sim: posso aceitar inconsistência eventual → AP.
                
            - Se não (ex: transação bancária crítica): preciso de consistência forte → CP.
                
3. **Ser explícito na escolha**:
    
    - CP: “prefiro falhar ou demorar, mas nunca mostrar dado inconsistente”.
        
    - AP: “prefiro responder sempre, mesmo correndo risco de inconsistência temporária”.
        
4. **Discutir mecanismos complementares**:
    
    - Em CP:
        
        - Consenso (Raft, Paxos).
            
        - Quórum de leitura/escrita.
            
        - Timeouts mais longos, possíveis “split-brain” e como evitá-los.
            
    - Em AP:
        
        - Estratégias de convergência: CRDTs, merges customizados, last-write-wins.
            
        - Detecção e resolução de conflitos.
            
        - Garantias de eventual consistency.
            

---

### 2.7. Erros de interpretação comuns

1. **“CAP é sobre performance”**  
    Não é. CAP é sobre possibilidade/impossibilidade de garantir propriedades na presença de partições.
    
2. **“Dá pra ter CA ignorando P e pronto”**  
    Em ambiente distribuído real, ignorar P é, na prática, dizer “quando der partição, estou disposto a parar tudo”. Isso é uma escolha, mas não elimina a impossibilidade, só “foge” do problema.
    
3. **“Meu sistema é CA porque uso banco relacional”**  
    Se esse banco está distribuído em várias máquinas, você precisa olhar o protocolo de replicação. Muitos setups de replicação assíncrona de banco relacional são, na verdade, AP (principal + replicas apenas para leitura, etc.), com consistência eventual.
    
4. **“Escolher AP significa abandono de consistência total”**  
    Não. Significa abandonar **consistência forte** (linearizável). Você ainda pode ter:
    
    - Consistência eventual.
        
    - Garantias causais.
        
    - Garantias por sessão (session consistency).
        

---

### 2.8. Resumo avançado em tom “técnico”

- CAP é um teorema de impossibilidade em sistemas distribuídos assíncronos com possibilidade de partições.
    
- “Consistência” em CAP é praticamente sinônimo de **linearizabilidade**.
    
- “Disponibilidade” é a garantia de resposta em tempo finito de qualquer nó não falho.
    
- “Partição” é perda arbitrária de comunicação entre grupos de nós.
    
- Diante de uma partição, um sistema não pode ser ao mesmo tempo:
    
    - Linearizável (C).
        
    - Totalmente disponível (A).