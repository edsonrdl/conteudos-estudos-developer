# Domain-Driven Design (DDD) #arquitetura #engenharia-software #design-patterns

## 1. Visão Geral
- [ ] Explicação resumida do DDD, histórico e propósito de alinhar o software ao negócio.
---
## 2. Pilares Fundamentais O Domínio e os Domain Experts Linguagem Ubíqua (Ubiquitous Language) Separação entre Design Estratégico e Tático
## 3. Design Estratégico (Espaço do Problema) 3.1 Subdomínios O que são Core Domain (Principal) Supporting Subdomain (Suporte) Generic Subdomain (Genérico) 3.2 Bounded Contexts (Contextos Delimitados) Explicação simples do limite de um modelo Como a Linguagem Ubíqua atua dentro do contexto 3.3 Context Mapping (Mapeamento de Contexto) Shared Kernel Customer-Supplier (Upstream/Downstream) Anticorruption Layer (ACL)
    
## 4. Design Tático (Espaço da Solução) 4.1 Entidades (Entities) Identidade única Ciclo de vida e mutabilidade 4.2 Objetos de Valor (Value Objects - VOs) Imutabilidade Comparação por atributos (ausência de identidade) 4.3 Agregados e Raízes de Agregação (Aggregate Roots) Fronteiras de consistência e transação Regras para persistência e comunicação externa 4.4 Repositórios (Repositories) Abstração da persistência (ocultando o banco de dados) Por que repósitórios só lidam com Aggregate Roots 4.5 Serviços de Domínio (Domain Services) Lógicas que não pertencem a uma Entidade ou VO específico 4.6 Eventos de Domínio (Domain Events) Representação de fatos passados no sistema
    
5. Trade-offs e Decisões de Arquitetura 5.1 Vantagens (Manutenibilidade, alinhamento com o negócio) 5.2 Desvantagens (Curva de aprendizado, complexidade inicial) 5.3 Quando usar (Microsserviços, domínios complexos) 5.4 Quando evitar (Aplicações CRUD simples)
    
6. Relacionamento com Outros Padrões DDD vs Arquitetura Hexagonal (Ports and Adapters) DDD vs Clean Architecture DDD na modelagem de Microsserviços
    
7. Exemplos Práticos Modelagem de "Produto" no Bounded Context de Vendas vs Bounded Context de Estoque Exemplo de organização de pastas/pacotes
    
8. Links Relacionados Arquitetura Hexagonal Microsserviços Clean Architecture CQRS Event-Driven Architecture (EDA)