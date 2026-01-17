## 1. Primeira verdade: a classificação é sobre **intenção**, não sobre forma

Quase todo pattern usa:

- **Objetos**,
    
- **Composição**,
    
- **Interfaces**.
    

Ou seja: pela **forma** (diagramas, classes, composição) muitos padrões parecem iguais.

O que os separa é:

> **Qual problema principal ele resolve?**  
> Esse “problema principal” é o que define se ele é criacional, estrutural ou comportamental.

- Se o foco é **como criar objetos** → Criacional.
    
- Se o foco é **como objetos se ligam / se organizam** → Estrutural.
    
- Se o foco é **como objetos se comportam / interagem** → Comportamental.
    

Guarda essa frase, porque ela é a bússola.

---

## 2. As 3 categorias em linguagem bem objetiva

### 2.1. Padrões Criacionais

Pergunta-chave:

> “O problema aqui é **criar objetos** de forma flexível, desacoplada ou controlada?”

Se sim, provavelmente é criacional.

Sintomas típicos:

- Você quer **esconder o `new`** de quem usa.
    
- Você quer **trocar a classe concreta** sem mudar o resto do sistema.
    
- Você precisa controlar **número de instâncias** (Singleton, Pool).
    
- Você não quer que o client conheça detalhes da construção (Builder, Abstract Factory).
    

Exemplos:

- `new` poluído demais → entra Factory / Abstract Factory.
    
- Um objeto que precisa ser configurado passo a passo → Builder.
    
- Reuso de instâncias → Prototype.
    

---

### 2.2. Padrões Estruturais

Pergunta-chave:

> “O problema aqui é **como encaixar/organizar objetos** uns com os outros?”

Se sim, tende a ser estrutural.

Sintomas típicos:

- Você já tem classes prontas e quer **usar juntas**, mas elas não encaixam → Adapter.
    
- Quer **simplificar** um subsistema grande e expor uma interface mais simples → Facade.
    
- Quer **decorar** um objeto com responsabilidades adicionais sem mexer na classe → Decorator.
    
- Quer representar **árvores** de objetos (parte-todo) → Composite.
    
- Quer separar **abstração** da implementação → Bridge.
    
- Quer economizar memória, compartilhando estado → Flyweight.
    
- Quer um objeto que **fique no meio** entre o cliente e o real → Proxy.
    

---

### 2.3. Padrões Comportamentais

Pergunta-chave:

> “O problema aqui é **como variar/compor comportamentos** e a forma como objetos conversam?”

Se sim, é comportamental.

Sintomas típicos:

- Você quer **trocar algoritmo** de forma plugável → Strategy.
    
- Objetos precisam ser **notificados** quando algo muda → Observer.
    
- Você tem um fluxo fixo com ganchos personalizáveis → Template Method.
    
- Você quer **encapsular comandos** como objetos → Command.
    
- O objeto muda de comportamento conforme o **estado interno** → State.
    
- Requisições em cadeia, onde cada handler pode tratar ou passar pra frente → Chain of Responsibility.
    
- Precisa percorrer coleções de forma padronizada → Iterator.
    
- Precisa centralizar comunicação entre muitos objetos → Mediator.
    
- etc.
    

---

## 3. “Mas e se meu Strategy parece estrutural?” – Aqui está a sutileza

Quase todo pattern comportamental usa **composição** (que é uma ideia estrutural).  
Então é normal você olhar e pensar: “isso aqui não é estrutural também?”

Resposta: **ele pode usar ideias estruturais**, mas a **classificação oficial** foca no _motivo de existir_.

### Exemplo: Strategy

`public interface CalculoFrete {     double calcular(double valor); }  public class FreteNormal implements CalculoFrete { ... } public class FreteExpresso implements CalculoFrete { ... }  public class Pedido {     private CalculoFrete calculoFrete; // composição      public Pedido(CalculoFrete calculoFrete) {         this.calculoFrete = calculoFrete;     }      public double calcularFrete(double valor) {         return calculoFrete.calcular(valor);     } }`

Por que ele é **comportamental**, e não estrutural?

- A preocupação dominante não é “como organizar objetos”, mas:
    
    > “Como **encapsular algoritmos** de frete e permitir trocá-los facilmente?”
    
- O eixo de variação é: **comportamento de cálculo**, não conexão entre classes.
    

Você **usa** composição (ideia estrutural) para **resolver um problema de comportamento**.  
Por isso Strategy é comportamental.

---

## 4. Como saber se você está “forçando” um padrão?

### 4.1. Sinais de que você está forçando o nome do pattern

1. **Você pensou no nome antes do problema**
    
    > “Quero usar Strategy aqui” em vez de “Tenho um problema de variação de comportamento”.
    
    O certo é:
    
    - primeiro: “que dor estou resolvendo?”
        
    - depois: “qual pattern parece com isso?”
        
2. **Você só tem 1 implementação “estratégia”**  
    Se você cria um Strategy mas só tem 1 classe concreta (e não há necessidade real de variar),  
    provavelmente está forçando.
    
3. **Nenhuma mudança futura fica mais fácil com esse pattern**  
    Se, tirando o pattern, nada ficaria de fato mais acoplado ou rígido,  
    talvez você esteja só complicando.
    
4. **Você está copiando o diagrama do livro, não o contexto do problema**  
    Isso vira “pattern-driven design”, o oposto de bom design.
    

---

### 4.2. Estratégia virando “estrutural” demais

Ela _parece_ estrutural quando:

- Você usa Strategy, mas na prática o que você queria era:
    
    - Adapter (adaptar interface de uma API antiga/externa),
        
    - Bridge (separar abstração e implementação, tipo `Device` + `Remote`),
        
    - ou até um simples **if/else** bastaria.
        

**Teste mental**:

> Se eu explicar esse pedaço do código para alguém, o que faz mais sentido dizer?
> 
> - “Aqui eu consigo **plugar algoritmos diferentes** para o mesmo problema” → Strategy (comportamental).
>     
> - “Aqui eu consigo **encaixar objetos diferentes**, cada um com interface própria, num cliente que não sabe quem é quem” → provavelmente estrutural (Adapter, Bridge, etc).
>     

---

## 5. Como definir a “barreira” na prática (checklist rápido)

Quando você olhar para uma solução e quiser classificá-la, faça esse mini-debug mental:

1. **Qual é o eixo principal de mudança que eu estou protegendo?**
    
    - Criação de objetos? → Criacional.
        
    - Organização/ligação entre objetos? → Estrutural.
        
    - Algoritmos / regras / fluxo / forma de interação? → Comportamental.
        
2. **Se amanhã o requisito mudar, o que vai mudar primeiro?**
    
    - “Vou precisar criar objetos de outro tipo” → criacional.
        
    - “Vou precisar conectar objetos diferentes / remover intermediários” → estrutural.
        
    - “Vou precisar mudar as regras / como processa / como reage” → comportamental.
        
3. **O benefício principal que vendo para alguém é qual?**
    
    - “Fica mais fácil trocar a forma de construir/instanciar” → criacional.
        
    - “Fica mais fácil agrupar/reorganizar componentes” → estrutural.
        
    - “Fica mais fácil trocar comportamento/regra” → comportamental.
        

A **barreira** é:

> _O que você está protegendo da mudança?_  
> Esse é o tipo do padrão.

---

## 6. Como argumentar em review / entrevista que “isso aqui é Strategy/comportamental”

Imagina que alguém fala:

> “Isso aí não é Strategy, é um pattern estrutural qualquer.”

Você responde apontando para **intenção e uso**, não para forma:

1. **Intenção**
    
    > “O objetivo aqui é encapsular algoritmos de cálculo de frete, para que eu possa trocar o comportamento sem mudar o contexto (`Pedido`).”
    
2. **Eixo de variação**
    
    > “O que varia são as regras de frete, não a forma como os objetos se conectam. A composição é só o mecanismo; o foco é o comportamento.”
    
3. **Evidência prática**
    
    - Hoje tenho `FreteNormal` e `FreteExpresso`.
        
    - Amanhã posso criar `FretePromocional` sem tocar na classe `Pedido`.
        
    - Posso inclusive escolher a estratégia em runtime de acordo com o cliente, país, etc.
        
4. **Ligação com a definição do GoF**  
    Strategy = _“Define a família de algoritmos, encapsula cada um deles e torna intercambiáveis”_.  
    Se o seu design faz exatamente isso, você tem um Strategy, mesmo que use composição, factories etc.
    

---

## 7. Patterns se combinam – e tá tudo bem

Outro ponto de especialista: **patterns raramente aparecem “puros”**.

Um mesmo trecho de código pode:

- Usar uma **Factory** para criar uma **Strategy**.
    
- Ter uma **Facade** que internamente delega para várias **Strategies**.
    
- Ter um **Composite** onde cada nó usa **State** internamente.
    

Então, mais importante que “carimbar” o pattern é:

- Você está **reduzindo acoplamento**?
    
- Facilitando **extensão sem mudar código existente**?
    
- Deixando o **eixo de mudança explícito**?
    

Se sim, você está na direção certa, mesmo que alguém discuta se “isso é mais X ou Y”.

---

## 8. Resumão pra você guardar (tipo mantra)

1. **Nome do pattern é consequência, não ponto de partida.**
    
2. Pergunte sempre:
    
    - problema de **criação** → criacional
        
    - problema de **ligação/estrutura** → estrutural
        
    - problema de **comportamento/interação** → comportamental
        
3. Strategy só “vira estrutural” na sua cabeça se você focar mais na composição do que na variação de algoritmo.
    
4. Em discussão/argumentação, sempre volte a:
    
    - intenção → eixo de variação → benefício concreto.