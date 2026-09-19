# 📚 Guia de Estudos: SLI/SLO/SLA
#engenharia-software #sre #metricas #confiabilidade

> [!info] Visão Geral
> SLI, SLO e SLA formam a cadeia que transforma "confiabilidade" de uma sensação subjetiva em algo medido, negociado e monitorado. Nascida da prática de Site Reliability Engineering (SRE) do Google, essa cadeia vai da métrica bruta observada no sistema (SLI), passando pela meta interna que o time se compromete a atingir (SLO), até o compromisso formal e contratual com o cliente (SLA) — cada nível com um propósito e uma audiência diferentes.

---

## 1 Domínio: Fundamentos e Motivação

### 1.1 Origem e Contexto
- **1.1.1. O Problema que SLI/SLO/SLA Resolvem**
  - [x] [[9. Engenharia de Software/Métricas e Qualidade/SLI-SLO-SLA/1. Fundamentos e Motivação/1.1 Origem e Contexto/1.1.1. O Problema que SLI-SLO-SLA Resolvem/1.1.1.1. Origem no Google SRE e o Problema que Resolvem|1.1.1.1. Origem no Google SRE e o Problema que Resolvem]]

---

## 2 Domínio: Os Três Pilares

### 2.1 SLI — Service Level Indicator
- **2.1.1. O que é e Como Medir**
  - [x] [[9. Engenharia de Software/Métricas e Qualidade/SLI-SLO-SLA/2. Os Três Pilares/2.1 SLI — Service Level Indicator/2.1.1. O que é e Como Medir/2.1.1.1. O que é um SLI e as Métricas mais Comuns|2.1.1.1. O que é um SLI e as Métricas mais Comuns]]
  - [x] [[9. Engenharia de Software/Métricas e Qualidade/SLI-SLO-SLA/2. Os Três Pilares/2.1 SLI — Service Level Indicator/2.1.1. O que é e Como Medir/2.1.1.2. Boas Práticas de Instrumentação - Onde e Quando Medir|2.1.1.2. Boas Práticas de Instrumentação - Onde e Quando Medir]]

### 2.2 SLO — Service Level Objective
- **2.2.1. Definindo Metas Realistas**
  - [ ] 2.2.1.1. Como escolher um SLO com base em dados históricos, não em desejos ou pressão comercial.
- **2.2.2. Error Budget**
  - [ ] 2.2.2.1. O orçamento de erro (Error Budget) e como ele equilibra velocidade de deploy com confiabilidade.

### 2.3 SLA — Service Level Agreement
- **2.3.1. O Contrato com o Cliente**
  - [ ] 2.3.1.1. SLA vs SLO: a diferença contratual, penalidades, e por que o SLA deve ser mais frouxo que o SLO interno.

---

## 3 Domínio: Relação entre os Três

### 3.1 Como SLI, SLO e SLA se Encaixam
- **3.1.1. A Cadeia de Confiabilidade**
  - [ ] 3.1.1.1. Fluxo completo: da métrica bruta (SLI) até a meta (SLO) e o compromisso contratual (SLA).

---

## 4 Domínio: Aplicação Prática

### 4.1 Monitoramento e Ferramentas
- **4.1.1. Implementando na Prática**
  - [ ] 4.1.1.1. Ferramentas comuns (Prometheus, Grafana, Google Cloud SLO Monitoring) e exemplo de configuração de SLO.

### 4.2 Alertas Baseados em Error Budget
- **4.2.1. Multiwindow, Multi-burn-rate Alerting**
  - [ ] 4.2.1.1. Como alertar sem gerar fadiga de alertas, usando a taxa de consumo do Error Budget.

---

## 5 Domínio: Erros Comuns e Limitações

### 5.1 Anti-padrões
- **5.1.1. Armadilhas Comuns**
  - [ ] 5.1.1.1. SLOs de 100%, métricas de vaidade, e a diferença entre medir o que importa para o usuário vs o que é fácil de medir.

---

> **Glossário Pai:** [[0 Glossário/Glossário|Base de Conhecimento Tecnológico]]
>
> **Links Relacionados:**
> [[9. Engenharia de Software/Métricas e Qualidade/0 Erros Críticos em Programação (Glossário)|Erros Críticos em Programação]]
