## [[10. Arquitetura de Software e Design de Software/Observabilidade/Correlation ID/1. Visão Geral.md|1. Visão Geral]]

> **Definição:** Um identificador único (geralmente um UUID/GUID) anexado a uma requisição no momento em que ela entra no sistema. Esse ID viaja com a requisição por todos os serviços, filas e bancos de dados.

**Objetivo:** Permitir o rastreamento de uma transação distribuída de ponta a ponta, agrupando logs de diferentes serviços que pertencem à mesma operação de negócio.

---

## 2. O Problema: "Agulha no Palheiro"

Em uma arquitetura de microsserviços, uma única ação do usuário (ex: "Checkout") pode acionar múltiplos serviços (Auth, Carrinho, Pagamento, Estoque).

- **Sem Correlation ID:** Se o pagamento falha, você tem logs espalhados em 4 servidores diferentes sem nada que os ligue.
    
- **Com Correlation ID:** Você filtra os logs pelo ID `a1b2-c3d4` e vê a história completa do erro.
    

---

## 3. Ciclo de Vida do ID

### 3.1 Geração (Ingress)

Quem deve criar o ID?

- **Client Side:** O Front-end gera e manda (útil para debugar problemas de rede/latência inicial).
    
- **Edge/Gateway:** O [[API Gateway]] ou [[Load Balance]] gera se o request chegar sem um.
    
- **Formato:** Geralmente UUID v4 (ex: `f47ac10b-58cc-4372-a567-0e02b2c3d479`).
    

### 3.2 Propagação (Context Propagation)

O ID deve ser repassado a cada salto ("hop"):

- **HTTP:** Via Headers.
    
- **Mensageria ([[Kafka]]/[[RabbitMQ]]):** Via Metadata/Headers da mensagem.
    
- **Background Jobs:** O ID deve ser persistido junto com o job.
    

### 3.3 Log (Append)

Todo log gerado pela aplicação deve incluir o ID automaticamente (usando bibliotecas de logging ou Middlewares).

---

## 4. Padrões de Headers

Não existe um padrão único, mas estes são os mais comuns:

- **Padrão de Mercado:** `X-Correlation-ID` ou `X-Request-ID`.
    
- **Padrão W3C/OpenTelemetry:** `Traceparent` (parte do padrão W3C Trace Context).
    
- **AWS:** `X-Amzn-Trace-Id`.
    

---

## 5. Diferença: Correlation ID vs Trace ID vs Span ID

Com a evolução da observabilidade ([[OpenTelemetry]]), os termos ficaram mais específicos:

- **Correlation ID:** Termo genérico para "ID de rastreio".
    
- **Trace ID:** O ID único da transação inteira (equivalente moderno ao Correlation ID).
    
- **Span ID:** O ID único de _uma etapa específica_ dentro do fluxo (ex: apenas o tempo que passou dentro do Microsserviço de Pagamento).
    

---

## 6. Desafios de Implementação

- **Filas Assíncronas:** É comum esquecer de repassar o header ao publicar uma mensagem no Kafka.
    
- **Threads:** Em linguagens como Java, é preciso garantir que o ID passe para novas Threads (MDC - Mapped Diagnostic Context).
    
- **Segurança:** Se o ID vier do cliente, deve ser sanitizado para evitar injeção de dados maliciosos.
    

---

## 7. Links Relacionados

- Tracing Distribuído - O conceito maior onde o Correlation ID se encaixa.
    
- OpenTelemetry - O padrão atual da indústria para rastreio.
    
- [Logs estruturados - Como armazenar esse ID (JSON vs Texto).
    
- API Gateway - O local ideal para gerar o ID.
    
- Arquitetura de Microsserviços