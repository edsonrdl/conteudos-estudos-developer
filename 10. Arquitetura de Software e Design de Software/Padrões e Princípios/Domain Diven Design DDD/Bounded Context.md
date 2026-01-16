O **Bounded Context** (Contexto Delimitado) é um conceito central no **Domain-Driven Design (DDD)**, que é uma abordagem para o desenvolvimento de software focado em domínios de negócio complexos.

Em essência, um Bounded Context é uma **fronteira lógica e conceitual** dentro de um sistema de software, onde um modelo de domínio específico é definido e aplicado consistentemente.

---

## 🎯 O Que Significa

- **Delimitação do Domínio:** Ele define os limites de um modelo de domínio. Dentro de um Bounded Context, os termos e conceitos (a **Linguagem Ubíqua**) têm um significado preciso e único.
    
    - **Exemplo:** Em um sistema de comércio eletrônico, a entidade **"Produto"** pode significar algo diferente em contextos distintos:
        
        - No **Contexto de Catálogo**, um "Produto" é algo com descrição, preço de varejo e imagem.
            
        - No **Contexto de Expedição (Entrega)**, o mesmo "Produto" é algo com peso, dimensões e código de localização no armazém.
            
- **Coerência e Consistência:** Garante que o modelo de domínio de uma área do negócio não seja poluído ou confuso pelos modelos de outras áreas. Isso torna o código mais **coerente**, **fácil de entender** e de **manter**.
    
- **Foco e Isolamento:** Cada Bounded Context é projetado para lidar com um **subdomínio** específico do negócio. Idealmente, ele é mantido por uma única equipe, permitindo o desenvolvimento e a evolução isolados, o que é crucial em arquiteturas de microsserviços.
    

---

## 🗺️ Mapa de Contexto (Context Map)

Para gerenciar a interação entre os diferentes Bounded Contexts em um sistema maior, o DDD sugere a criação de um **Mapa de Contexto (Context Map)** , que é uma representação visual das relações e integrações entre eles (por exemplo, Cliente/Fornecedor, Linguagem Compartilhada, etc.).