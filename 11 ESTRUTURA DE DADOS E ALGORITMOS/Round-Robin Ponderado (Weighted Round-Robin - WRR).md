O Round-Robin Ponderado é um algoritmo de balanceamento de carga estático que é **particularmente útil em estratégias de migração (como o "Cutover" ou "Blue/Green")** onde você tem servidores com capacidades diferentes ou deseja direcionar uma quantidade maior de tráfego para um conjunto específico de servidores, como os novos.

#### Como Funciona:

1. **Atribuição de Pesos (Weights):** Cada servidor no _pool_ de balanceamento de carga recebe um **"peso"** (um valor numérico) que reflete sua capacidade, prioridade, ou, no caso de migração, a porcentagem de tráfego que ele deve receber.
    
    - Servidores com **peso mais alto** recebem **mais requisições**.
        
    - Servidores com **peso mais baixo** recebem **menos requisições**.
        
2. **Distribuição Proporcional:** O balanceador de carga distribui as requisições em uma rotação sequencial, mas a frequência com que cada servidor é selecionado é **proporcional** ao seu peso.
    

#### 💡 Aplicação em Estratégias de Migração:

Em uma estratégia de migração, como a migração de um servidor (ou grupo de servidores) antigo para um novo, o Round-Robin Ponderado permite um controle gradual e seguro do tráfego.

|**Etapa da Migração**|**Configuração de Pesos (Exemplo)**|**Objetivo**|
|---|---|---|
|**Fase 1: Teste Inicial**|Servidores Antigos: **10** / Servidores Novos: **1**|Direcionar um volume **mínimo** de tráfego para os novos servidores para testes de sanidade e performance, mantendo a maioria da carga no ambiente antigo.|
|**Fase 2: Transição Lenta**|Servidores Antigos: **5** / Servidores Novos: **5**|Distribuir o tráfego de forma **equilibrada** (50/50) enquanto se monitora o desempenho dos novos servidores sob carga real.|
|**Fase 3: Pré-Cutover**|Servidores Antigos: **1** / Servidores Novos: **10**|Direcionar a **maioria** do tráfego para os novos servidores, garantindo que o ambiente antigo ainda possa receber uma pequena parcela caso haja um problema crítico no novo ambiente (opção de rollback rápido).|
|**Fase 4: Migração Concluída**|Servidores Antigos: **0** / Servidores Novos: **10**|O ambiente antigo é desativado (peso zero) e **todo o tráfego** é direcionado para os novos servidores.|

---

### Outros Algoritmos Ponderados Relevantes

Embora o WRR seja o mais direto para uma distribuição gradual, há variantes dinâmicas que podem ser úteis se as capacidades dos servidores mudarem em tempo real:

- **Menos Conexões Ponderado (Weighted Least Connections - WLC):** Este algoritmo combina a ideia do peso com a carga de trabalho atual do servidor. Ele direciona o tráfego para o servidor com a **menor proporção** de conexões ativas em relação ao seu peso. É útil para sistemas com conexões de longa duração.
    
- **Menor Tempo de Resposta Ponderado (Weighted Least Response Time):** Direciona o tráfego para o servidor que tem o menor número de conexões ativas E o menor tempo médio de resposta, levando em conta o peso atribuído.
    

O **Round-Robin Ponderado** é geralmente a escolha padrão para a estratégia de **migração gradual** por sua simplicidade, previsibilidade e controle rígido sobre a proporção de tráfego que cada conjunto de servidores recebe.