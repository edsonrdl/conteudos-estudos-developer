## O Prompt Mestre para Arquitetura
"Atue como um Arquiteto de Software Senior e Especialista em Cloud. Vou te perguntar sobre **[TÓPICO]**. Não quero uma introdução básica. Forneça uma análise técnica profunda dividida nos seguintes tópicos:

1. **Características Técnicas e Funcionamento Interno:** Como a tecnologia opera sob o capô (ex: protocolos, persistência, garantias de entrega).
    
2. **Vantagens vs. Desvantagens (Trade-offs):** Liste benefícios claros e, principalmente, os pontos de dor ou limitações técnicas.
    
3. **Cenários de Arquitetura:** Descreva 2 cenários reais de uso (um simples e um complexo/distribuído) e 1 cenário onde essa tecnologia **não** deve ser usada.
    
4. **Limitações e Quotas:** Detalhe gargalos de performance, limites de payload, latência esperada e custos ocultos.
    
5. **Comparativo de Mercado:** Compare brevemente com a alternativa mais próxima (ex: SNS vs SQS ou SNS vs Kafka).
    

O tópico é: **[INSIRA O ASSUNTO AQUI]**"

Dicas extras para suas pesquisas:

- **Peça o "Happy Path" e o "Failure Mode":** Sempre pergunte o que acontece quando o serviço falha. Como ele lida com retentativas (retries) e Dead Letter Queues (DLQ)?
    
- **Contexto de Custo:** Como arquiteto, o custo é uma métrica técnica. Peça para eu explicar como a cobrança escala (ex: por milhão de solicitações vs. provisionamento fixo).
    
- **Nível de Abstração:** Se estiver estudando uma linguagem, peça para analisar o uso de memória (Heap vs Stack) ou o impacto do Garbage Collector naquele recurso específico.
- 

## O Prompt de Arquitetura de Baixo Nível

> "Atue como um Arquiteto de Software e Engenheiro de Sistemas Senior. Analise o **[TÓPICO]** sob a ótica de engenharia reversa e design de sistemas, cobrindo:
> 
> 1. **Estruturas de Dados:** Quais estruturas ele usa internamente para garantir performance? (Ex: B-Trees, LSM-Trees, Ring Buffers, Hash Tables).
>     
> 2. **Design Patterns:** Quais padrões de projeto são aplicados na arquitetura dessa ferramenta/linguagem? (Ex: Circuit Breaker, Singleton, Strategy, Pub/Sub).
>     
> 3. **Comportamento de Dados:** Como ele lida com a ordem e consistência? (Ex: É estritamente FIFO? É Eventual Consistency ou Strong Consistency?).
>     
> 4. **Modelo de Concorrência e IO:** Como ele gerencia Threads e Entrada/Saída? (Ex: Blocking vs Non-blocking, Event Loop).
>     
> 5. **Cenário de Falha:** Descreva o comportamento do sistema quando um nó cai ou a rede oscila (Teorema CAP).

## Prompt de estudos
Atue como um Arquiteto de Software Senior da AWS. Crie um desafio técnico baseado em um cenário real de falha de arquitetura (ex: gargalo de banco de dados, latência em sistemas distribuídos ou falha de segurança em VPC). Forneça 5 questões de múltipla escolha no estilo da prova SAA-C03, exigindo que eu escolha a solução mais resiliente e econômica. Para cada resposta, inclua uma justificativa técnica detalhada comparando os serviços envolvidos.