# 📚 Guia de Estudos: Teorema CAP (Consistency, Availability, Partition Tolerance)
#arquitetura #sistemas-distribuidos #cap #consistencia #disponibilidade

> [!info] Visão Geral
> O Teorema CAP é um teorema de impossibilidade sobre sistemas distribuídos. Ele enuncia uma limitação fundamental: diante de uma partição de rede, um sistema distribuído é obrigado a escolher entre manter **Consistência forte (C)** ou **Disponibilidade (A)** — não pode garantir as duas simultaneamente. Formulado como conjectura por Eric Brewer (2000) e provado formalmente por Gilbert e Lynch (2002), o CAP é a base para entender os trade-offs de qualquer banco distribuído, cache, mensageria ou arquitetura de microsserviços.

![[img-teorema-cap.png]]

---

## 1 Domínio: Fundamentos e Contexto

### 1.1 O que é e por que existe
- **1.1.1. Sistemas distribuídos**
  - [ ] 1.1.1.1. O que são sistemas distribuídos e por que o CAP existe — motivações (escala, disponibilidade, latência, resiliência) e os problemas que surgem ao distribuir.

### 1.2 As três propriedades
- **1.2.1. C, A e P**
  - [ ] 1.2.1.1. Consistência, Disponibilidade e Tolerância a Partições — o significado preciso de cada letra no contexto CAP (linearizabilidade, resposta em tempo finito, partição de rede).

---

## 2 Domínio: O Enunciado e as Escolhas

### 2.1 O teorema
- **2.1.1. Enunciado e intuição**
  - [ ] 2.1.1.1. O enunciado do teorema e a intuição com e sem partição — os cenários que revelam a impossibilidade.

### 2.2 CP vs AP
- **2.2.1. As duas escolhas**
  - [ ] 2.2.1.1. Sistemas CP vs AP — o trade-off sob partição, resolução de conflitos, o mito do "CA" e exemplos reais.

---

## 3 Domínio: Formalização e Extensões

### 3.1 Prova e nuances
- **3.1.1. Origem formal**
  - [ ] 3.1.1.1. Prova formal (Gilbert & Lynch), linearizabilidade em detalhe e o que o CAP não fala (performance, latência, throughput).

### 3.2 PACELC
- **3.2.1. Além da partição**
  - [ ] 3.2.1.1. CAP vs PACELC — o trade-off latência vs consistência mesmo sem partição, com exemplos (Dynamo, Cassandra, Spanner).

---

## 4 Domínio: CAP na Prática

### 4.1 Implicações arquiteturais
- **4.1.1. Aplicação real**
  - [ ] 4.1.1.1. CAP em microsserviços, caches, bancos distribuídos e quóruns N/W/R — como o teorema se manifesta em decisões concretas de arquitetura.

### 4.2 Raciocínio e erros
- **4.2.1. CAP awareness**
  - [ ] 4.2.1.1. CAP awareness — como decidir CP ou AP caso a caso, mecanismos complementares e os erros de interpretação mais comuns.

---

> **Glossário Pai:** [[0 Glossário/Glossário|Base de Conhecimento Tecnológico]]
>
> **Links Relacionados:**
> [[10. Arquitetura de Software e Design de Software/Dados e Consistência/PALELC Teorema|Teorema PACELC]]
> [[10. Arquitetura de Software e Design de Software/Dados e Consistência/Transactional Outbox Pattern|Transactional Outbox Pattern]]
> [[10. Arquitetura de Software e Design de Software/Padrões e Princípios/Padrões Arquiteturais/Microservices/1. O que é Microservices|Microservices]]
