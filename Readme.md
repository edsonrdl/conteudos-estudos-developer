## Estudos sobre tecnologias desenvolvimentos de software
# 🧠 Base de Conhecimento: Arquitetura e Engenharia de Software

![Status](https://img.shields.io/badge/Status-Ativo_e_em_Evolução-success?style=for-the-badge)
![Foco](https://img.shields.io/badge/Foco-Arquitetura_&_Nuvem-blue?style=for-the-badge)

## 🎯 Objetivo do Repositório
Este "Segundo Cérebro" centraliza estudos aprofundados, documentação de decisões arquiteturais e guias de referência sobre desenvolvimento de software, infraestrutura e metodologias. 

O foco aqui **não é** armazenar tutoriais rasos ou sintaxes básicas, mas sim documentar o "como as coisas funcionam por baixo do capô", análises comparativas e fundamentos críticos que sustentam aplicações resilientes, escaláveis e seguras.

## 🏢 Aplicação Prática
O conhecimento estruturado neste repositório alimenta diretamente:
- **Decisões de Produção (ASI):** Padronização técnica, infraestrutura como código (IaC) e escolhas arquiteturais seguras por design para projetos da fábrica de software.
- **Sistemas de Alta Disponibilidade:** Fundamentação para arquitetar plataformas que exigem alta simultaneidade e absorção de picos de carga (ex: plataformas de simulados e vestibulares).
- **Mentoria (Projeto Trajetoria-Dev):** Atua como o mapa de navegação técnico definitivo para acelerar o *onboarding* e o desenvolvimento de engenheiros juniores, focando no entendimento de vantagens e desvantagens de cada tecnologia.
- **Validação em Laboratório:** Documentação de cenários de teste simulados e homologados em servidores locais (cluster Proxmox) antes da migração para a nuvem pública (AWS).

---

## 📂 Estrutura de Domínios (Glossário Geral)
A taxonomia de pastas e notas segue rigorosamente a divisão abaixo. Tecnologias específicas são aprofundadas como "nós filhos" dentro destas categorias raiz:

1. **Redes e Infraestrutura:** *TCP/IP, DNS, Load Balancers, Service Mesh.*
2. **Protocolos e APIs:** *REST, gRPC, WebSockets, SOAP.*
3. **Desenvolvimento Front-End:** *PWA, React, Angular, Vue.*
4. **Design e Experiência (UX/UI):** *Usabilidade, Figma.*
5. **Desenvolvimento Back-End:** *Microsserviços, Clean Architecture, Padrões de Fluxo (Middlewares).*
6. **Mobile Development:** *Kotlin, React Native, Flutter.*
7. **Bancos de Dados e Armazenamento:** *SQL, NoSQL, Sharding, Data Lakes.*
8. **DevOps e Cultura Cloud-Native:** *Docker, Kubernetes, AWS Core, CI/CD, Observabilidade.*
9. **Engenharia de Software:** *Qualidade, SLI/SLO, Ciclo de Vida.*
10. **Arquitetura de Software e Design:** *DDD, SOLID, Mensageria, Resiliência (Circuit Breaker, Retry).*
11. **Estruturas de Dados e Algoritmos:** *Busca, Ordenação, Complexidade O(n).*
12. **Plataformas e Sistemas Operacionais:** *Linux, Windows.*
13. **Segurança da Informação:** *Criptografia, JWT, TLS, Proteção de Dados.*
14. **Inteligência Artificial e ML:** *Redes Neurais, Visão Computacional.*
15. **Data Analytics e BI:** *Pandas, Power BI.*
16. **Pipelines de Dados e ETL:** *Apache Spark, Kafka.*
17. **Automação e RPA:** *Selenium, BotCity.*
18. **Testes Automatizados:** *Unitários, E2E, Testes de Carga (K6).*

---

## 📐 Diretrizes Arquiteturais para Novas Notas

Sempre que uma nova tecnologia ou conceito for adicionado a esta base, a nota correspondente (Glossário Filho) deve obrigatoriamente seguir este padrão de excelência:

1. **Profundidade Técnica:** Vá direto ao ponto técnico. Explique o motor interno e o ciclo de vida da tecnologia.
2. **Análise Comparativa Nativa:** Não crie seções isoladas de "Trade-offs". As vantagens e desvantagens (custos computacionais, manutenção, complexidade) devem estar embutidas na explicação técnica de como a ferramenta funciona.
3. **Analogias Estratégicas:** Use analogias do mundo real **apenas** para ancorar conceitos de alta abstração. Mantenha o foco técnico para assuntos cotidianos.
4. **Cenários do Dia a Dia:** Todo conceito deve ter um exemplo prático focado nos desafios reais que uma empresa ou desenvolvedor enfrenta em produção.
5. **Hierarquia Estrita:** Utilize o formato de tópicos colapsáveis (1Tem , 1.1, 1.1.1) utilizando cabeçalhos e listas identadas de Markdown para garantir a escaneabilidade.

---
*Mantido por Edson Lopes — Arquiteto de Software*