O **Domain-Driven Design (DDD)**, ou Design Orientado a Domínio, é uma **abordagem (filosofia ou conjunto de princípios)** para o desenvolvimento de software que coloca o **domínio de negócio** e a sua complexidade no **centro** do projeto.

Não é uma tecnologia, uma metodologia ágil específica (como Scrum) ou um padrão de arquitetura (como MVC), mas sim um _mindset_ que afeta a forma como pensamos, comunicamos e estruturamos o código para que ele reflita fielmente o negócio.

## 🎯 O Foco Principal do DDD

O DDD foi criado por **Eric Evans**, em seu livro de 2003, para auxiliar equipes a lidar com a **complexidade** de grandes sistemas de software, garantindo que o software realmente resolva os problemas do **negócio (o Domínio)**.

1. **Domínio:** É o campo de conhecimento, regras, processos e atividades que o software deve modelar e automatizar. É a razão pela qual o software existe.
    
2. **Modelagem de Domínio:** A criação de um modelo que representa a essência do negócio de forma abstrata. O software deve ser uma **tradução fiel** e expressiva desse modelo.
    
3. **Comunicação:** O DDD insiste em uma comunicação contínua e eficaz entre os **Especialistas de Domínio (os "clientes" ou usuários que conhecem o negócio)** e os **Desenvolvedores**.
    

---

## 🔑 Pilares e Conceitos Chave

O DDD é dividido em duas partes principais: **Design Estratégico** (modelagem do sistema em grande escala) e **Design Tático** (padrões de implementação de código).

### 1. Design Estratégico (Visão Geral do Sistema)

Essa parte lida com a divisão e organização do sistema.

|**Conceito**|**Descrição**|
|---|---|
|**Domínio**|A área de atuação do negócio. (Exemplo: "E-commerce").|
|**Subdomínio**|Divisões lógicas do Domínio. Podem ser **Core** (principal), **Suporte** (necessário, mas não central) ou **Genérico** (soluções prontas). (Exemplo: "Pagamento", "Catálogo", "Logística").|
|**Bounded Context (Contexto Delimitado)**|A **fronteira lógica** onde um modelo de domínio específico é aplicado. É o limite do seu modelo de domínio. O conceito "Produto" tem um significado diferente no Bounded Context de "Catálogo" e no de "Logística".|
|**Linguagem Ubíqua (Ubiquitous Language)**|Um vocabulário **único** e **consistente** compartilhado por **todos** (especialistas de negócio e desenvolvedores) dentro de um Bounded Context. Os termos do código, da documentação e da conversa são os mesmos.|
|**Context Map (Mapa de Contexto)**|Uma visão geral que mostra todos os **Bounded Contexts** do sistema e como eles se relacionam e se integram entre si.|

### 2. Design Tático (Estrutura do Código)

Essa parte lida com a organização do código dentro de um **Bounded Context** para torná-lo expressivo e fiel ao modelo de domínio.

|**Padrão**|**Descrição**|
|---|---|
|**Entity (Entidade)**|Objetos que possuem uma **identidade única** e um ciclo de vida longo. São mutáveis. (Exemplo: `Cliente` com um `ID` único).|
|**Value Object (Objeto de Valor)**|Objetos que descrevem algo **imprescindível**, mas **não** possuem identidade própria; são definidos por seus atributos e são **imutáveis**. (Exemplo: `Dinheiro` (valor e moeda), `Endereço`).|
|**Aggregate (Agregado)**|Um **cluster** de Entidades e/ou Value Objects que são tratados como uma **unidade transacional**. Possui uma **Raiz do Agregado** (Aggregate Root), que é a única entidade que pode ser acessada e manipulada de fora do agregado, garantindo a consistência das regras de negócio. (Exemplo: `Pedido` como Raiz, que contém as `LinhasDeItem`).|
|**Repository (Repositório)**|Abstrai a lógica de persistência (armazenamento e recuperação de dados) para que o código de domínio não se preocupe com a tecnologia de banco de dados.|
|**Domain Service (Serviço de Domínio)**|Objetos que encapsulam uma **lógica de negócio** que não pertence naturalmente a nenhuma Entidade ou Value Object. (Exemplo: Processo de transferência de fundos entre duas contas).|
|**Domain Event (Evento de Domínio)**|Representa algo significativo que **aconteceu** no domínio e que outras partes do sistema (ou outros Bounded Contexts) podem estar interessadas em reagir. (Exemplo: `PedidoFoiRealizado`).|

---

## 📘 Documentação Completa (Referência Principal)

A documentação "completa" do Domain-Driven Design está no livro original e principal referência:

> **Livro:** **_Domain-Driven Design: Tackling Complexity in the Heart of Software_** (Design Orientado a Domínio: Atacando a Complexidade no Coração do Software)
> 
> **Autor:** **Eric Evans**
> 
> _Este livro é a fonte definitiva e mais detalhada de todos os conceitos, padrões e princípios do DDD._

Para uma visão mais moderna, com foco em arquiteturas baseadas em mensagens, Event Sourcing e CQRS, a comunidade também recomenda o livro:

> **Livro:** **_Implementing Domain-Driven Design_** (Implementando Domain-Driven Design)
> 
> **Autor:** **Vaughn Vernon**