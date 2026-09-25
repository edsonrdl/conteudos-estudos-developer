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
  - [x] [[9. Engenharia de Software/Métricas e Qualidade/SLI-SLO-SLA/2. Os Três Pilares/2.2 SLO — Service Level Objective/2.2.1. Definindo Metas Realistas/2.2.1.1. Como Definir um SLO com Base em Dados Históricos|2.2.1.1. Como Definir um SLO com Base em Dados Históricos]]
- **2.2.2. Error Budget**
  - [x] [[9. Engenharia de Software/Métricas e Qualidade/SLI-SLO-SLA/2. Os Três Pilares/2.2 SLO — Service Level Objective/2.2.2. Error Budget/2.2.2.1. Error Budget - O Orçamento de Erro|2.2.2.1. Error Budget - O Orçamento de Erro]]

### 2.3 SLA — Service Level Agreement
- **2.3.1. O Contrato com o Cliente**
  - [x] [[9. Engenharia de Software/Métricas e Qualidade/SLI-SLO-SLA/2. Os Três Pilares/2.3 SLA — Service Level Agreement/2.3.1. O Contrato com o Cliente/2.3.1.1. SLA vs SLO - Diferença Contratual e Margem de Segurança|2.3.1.1. SLA vs SLO - Diferença Contratual e Margem de Segurança]]

---

## 3 Domínio: Relação entre os Três

### 3.1 Como SLI, SLO e SLA se Encaixam
- **3.1.1. A Cadeia de Confiabilidade**
  - [x] [[9. Engenharia de Software/Métricas e Qualidade/SLI-SLO-SLA/3. Relação entre os Três/3.1 Como SLI, SLO e SLA se Encaixam/3.1.1. A Cadeia de Confiabilidade/3.1.1.1. Fluxo Completo - Da Métrica Bruta ao Compromisso Contratual|3.1.1.1. Fluxo Completo - Da Métrica Bruta ao Compromisso Contratual]]

---

## 4 Domínio: Aplicação Prática

### 4.1 Monitoramento e Ferramentas
- **4.1.1. Implementando na Prática**
  - [x] [[9. Engenharia de Software/Métricas e Qualidade/SLI-SLO-SLA/4. Aplicação Prática/4.1 Monitoramento e Ferramentas/4.1.1. Implementando na Prática/4.1.1.1. Ferramentas - Prometheus, Grafana e Google Cloud SLO Monitoring|4.1.1.1. Ferramentas - Prometheus, Grafana e Google Cloud SLO Monitoring]]

### 4.2 Alertas Baseados em Error Budget
- **4.2.1. Multiwindow, Multi-burn-rate Alerting**
  - [x] [[9. Engenharia de Software/Métricas e Qualidade/SLI-SLO-SLA/4. Aplicação Prática/4.2 Alertas Baseados em Error Budget/4.2.1. Multiwindow, Multi-burn-rate Alerting/4.2.1.1. Alertas sem Fadiga - Multiwindow, Multi-burn-rate|4.2.1.1. Alertas sem Fadiga - Multiwindow, Multi-burn-rate]]

---

## 5 Domínio: Erros Comuns e Limitações

### 5.1 Anti-padrões
- **5.1.1. Armadilhas Comuns**
  - [x] [[9. Engenharia de Software/Métricas e Qualidade/SLI-SLO-SLA/5. Erros Comuns e Limitações/5.1 Anti-padrões/5.1.1. Armadilhas Comuns/5.1.1.1. SLOs de 100%, Métricas de Vaidade e Medir o Fácil vs o Importante|5.1.1.1. SLOs de 100%, Métricas de Vaidade e Medir o Fácil vs o Importante]]

---

> **Glossário Pai:** [[0 Glossário/Glossário|Base de Conhecimento Tecnológico]]
>
> **Links Relacionados:**
> [[9. Engenharia de Software/Métricas e Qualidade/0 Erros Críticos em Programação (Glossário)|Erros Críticos em Programação]]
