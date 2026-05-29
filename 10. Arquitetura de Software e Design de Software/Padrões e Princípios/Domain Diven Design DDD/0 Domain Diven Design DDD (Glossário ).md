# 📚 Guia de Estudos: Domain-Driven Design (DDD)

> [!info] Visão Geral
> O Domain-Driven Design (DDD), idealizado por Eric Evans, é uma abordagem de desenvolvimento de software cujo propósito primário é alinhar a implementação técnica do código diretamente com as regras e realidades do negócio. Ele não é uma arquitetura de software (como MVC ou Clean), mas sim um conjunto de práticas e padrões que visa modelar domínios complexos, garantindo que desenvolvedores e especialistas de negócio falem exatamente a mesma língua.

---

## 1 Domínio: Pilares Fundamentais do DDD

### 1.1 Alicerces da Modelagem
- **1.1.1. O Domínio e os Domain Experts**
  - 1.1.1.1. O Domínio é o coração do negócio (o problema a ser resolvido).
  - 1.1.1.2. Os *Domain Experts* são as pessoas que entendem profundamente as regras (especialistas, analistas, usuários chave) e devem colaborar constantemente com os desenvolvedores.
- **1.1.2. Linguagem Ubíqua (Ubiquitous Language)**
  - 1.1.2.1. Uma linguagem rigorosa, comum e sem ambiguidades, construída em conjunto pela equipe técnica e de negócio.
  - 1.1.2.2. Os termos definidos na Linguagem Ubíqua devem ser refletidos **exatamente da mesma forma** no código-fonte (nomes de classes, variáveis e métodos).
- **1.1.3. Separação de Preocupações (Estratégico vs Tático)**
  - 1.1.3.1. **Estratégico:** Focado em "O Quê" e "Onde" (divisão do problema em contextos e equipes).
  - 1.1.3.2. **Tático:** Focado em "Como" (padrões de código para implementar a lógica rica).

---

## 2 Domínio: Design Estratégico (Espaço do Problema)

### 2.1 Subdomínios (Subdomains)
- **2.1.1. Core Domain (Domínio Principal)**
  - 2.1.1.1. O diferencial competitivo da empresa. Onde o sistema traz mais valor e onde os melhores desenvolvedores devem atuar.
- **2.1.2. Supporting Subdomain (Domínio de Suporte)**
  - 2.1.2.1. Funcionalidades necessárias para o negócio funcionar, mas que não são o diferencial central (ex: sistema de catálogo).
- **2.1.3. Generic Subdomain (Domínio Genérico)**
  - 2.1.3.1. Problemas complexos, mas que já foram resolvidos pelo mercado. Ideal para comprar pronto ou usar soluções Open Source (ex: sistema de autenticação, faturamento).

### 2.2 Bounded Contexts (Contextos Delimitados)
- **2.2.1. Fronteiras de Limite Escopo**
  - 2.2.1.1. É a fronteira explícita (física ou lógica) dentro da qual um modelo de domínio específico e uma Linguagem Ubíqua têm validade absoluta.
- **2.2.2. Autonomia**
  - 2.2.2.1. Um modelo que faz sentido no Contexto A pode ter atributos e regras completamente diferentes no Contexto B, mesmo que usem a mesma palavra.

### 2.3 Context Mapping (Mapeamento de Contexto)
- **2.3.1. Padrões de Integração entre Bounded Contexts**
  - 2.3.1.1. **Shared Kernel:** Um núcleo compartilhado de código/modelo onde alterações afetam ambos os times. Requer alta sincronia.
  - 2.3.1.2. **Customer-Supplier (Upstream/Downstream):** Um contexto (Fornecedor/Upstream) fornece dados que o outro contexto (Cliente/Downstream) consome.
  - 2.3.1.3. **Anticorruption Layer (ACL):** Camada de tradução que protege um contexto de ser "poluído" pelas regras ou modelos estruturais de um contexto externo ou legado.

---

## 3 Domínio: Design Tático (Espaço da Solução)

### 3.1 Entidades e Objetos de Valor
- **3.1.1. Entidades (Entities)**
  - 3.1.1.1. Objetos que possuem uma **Identidade Única** (ID) que os distingue.
  - 3.1.1.2. Possuem ciclo de vida longo e seus atributos podem ser mutáveis com o tempo (ex: `User`, `Order`).
- **3.1.2. Objetos de Valor (Value Objects - VOs)**
  - 3.1.2.1. Objetos descritivos que **não possuem identidade própria**. São comparados apenas por seus atributos (ex: `Money`, `Address`, `CPF`).
  - 3.1.2.2. Devem ser projetados como **Imutáveis**. Qualquer alteração requer a criação de um novo objeto.

### 3.2 Agregados e Repositórios
- **3.2.1. Agregados e Raízes de Agregação (Aggregate Roots)**
  - 3.2.1.1. Um Agregado é um cluster (conjunto) de Entidades e VOs tratados como uma única unidade de modificação de dados.
  - 3.2.1.2. **Fronteira de Consistência:** Toda transação de banco de dados deve alterar apenas um Agregado por vez.
  - 3.2.1.3. **Aggregate Root:** A Entidade principal do Agregado. Objetos externos só podem guardar referências para a Raiz, nunca para os itens internos do Agregado.
- **3.2.2. Repositórios (Repositories)**
  - 3.2.2.1. Abstrações que fingem ser coleções de objetos em memória, ocultando a complexidade de persistência (DB, APIs).
  - 3.2.2.2. **Regra de Ouro:** Repositórios existem **apenas** para Raízes de Agregação (Aggregate Roots).

### 3.3 Serviços e Eventos
- **3.3.1. Serviços de Domínio (Domain Services)**
  - 3.3.1.1. Lógicas de negócio que não pertencem naturalmente a uma única Entidade ou VO (ex: transferência de dinheiro entre duas Contas).
- **3.3.2. Eventos de Domínio (Domain Events)**
  - 3.3.2.1. Representação de fatos relevantes para o negócio que **já ocorreram** no sistema (ex: `OrderConfirmed`, `PaymentFailed`).
  - 3.3.2.2. Usados intensamente para disparar reações em outros Bounded Contexts de forma assíncrona.

---

## 4 Domínio: Decisões Arquiteturais e Trade-offs

### 4.1 Vantagens e Desvantagens (Análise Nativa)
- **4.1.1. Otimizações e Ganhos**
  - 4.1.1.1. Alta manutenibilidade a longo prazo.
  - 4.1.1.2. Forte alinhamento cognitivo entre a equipe de desenvolvimento e as partes interessadas do negócio.
- **4.1.2. Custos Computacionais e Operacionais**
  - 4.1.2.1. Curva de aprendizado extremamente íngreme para a equipe.
  - 4.1.2.2. Complexidade inicial muito elevada (excesso de abstrações) que atrasa a primeira entrega.

### 4.2 Critérios de Adoção
- **4.2.1. Quando Usar**
  - 4.2.1.1. Em domínios de negócio altamente complexos, repletos de regras específicas e mutáveis.
  - 4.2.1.2. Como fundação para a divisão de bases de código em Microsserviços baseados em Bounded Contexts.
- **4.2.2. Quando Evitar**
  - 4.2.2.1. Aplicações puramente CRUD (Create, Read, Update, Delete) ou sistemas focados apenas em armazenamento de dados sem regras ricas (*data-driven*).

---

## 5 Domínio: Relacionamento com Outros Padrões e Exemplos

### 5.1 Relacionamento com Arquiteturas Modernas
- **5.1.1. DDD vs Arquitetura Hexagonal (Ports and Adapters)**
  - 5.1.1.1. Complementares. A Hexagonal blinda o modelo rico do DDD (no centro) contra frameworks e infraestrutura externa usando portas (interfaces).
- **5.1.2. DDD vs Clean Architecture**
  - 5.1.2.1. A Clean Architecture fornece as "camadas" concêntricas ideais (Entities e Use Cases) para abrigar o Design Tático do DDD.
- **5.1.3. DDD e Microsserviços**
  - 5.1.3.1. Bounded Contexts são considerados a régua ideal para definir as fronteiras físicas de um Microsserviço, evitando sistemas monolíticos distribuídos.

### 5.2 Exemplos Práticos de Modelagem
- **5.2.1. Modelagem Multi-Contexto ("Produto")**
  - 5.2.1.1. No Bounded Context de **Vendas**, a Entidade `Product` tem atributos como `preco`, `taxa_desconto` e `promocao_ativa`.
  - 5.2.1.2. No Bounded Context de **Estoque/Logística**, a Entidade `Product` tem atributos como `peso`, `dimensoes` e `localizacao_corredor`.
  - 5.2.1.3. O DDD aceita que a mesma palavra signifique e comporte coisas diferentes em contextos diferentes, evitando uma classe gigante de Produto para tudo.
- **5.2.2. Organização Típica de Pastas**
  - 5.2.2.1. O design orienta agrupar por negócios (`/sales`, `/inventory`, `/billing`) em vez de agrupar tecnicamente (`/controllers`, `/services`, `/models`).

---
> **Links Relacionados:** 
> Arquitetura Hexagonal
> Microservices
> Clean architecture
> CQRS
>  Event-Driven Architecture