Calcular volumetria para implantações de LLM locais exige uma mudança drástica de paradigma arquitetural. No mundo das APIs REST tradicionais, nós dimensionamos servidores baseados em Requisições por Segundo (RPS) e gargalos de I/O. No mundo da IA Generativa, o gargalo é quase exclusivamente a **Largura de Banda de Memória (Memory Bandwidth)** e a **VRAM (Memória de Vídeo)**.

Para uma PME, onde o número de funcionários não está na casa dos milhares, o cálculo não se baseia no volume mensal, mas sim no **pico de concorrência e no tamanho do contexto injetado pelo RAG**.

Abaixo, detalho como você, como Arquiteto, deve estruturar esse cálculo matemático e operacional.

---

## 1. O Levantamento das Variáveis de Carga (O Input)

Antes de olhar para o hardware, você precisa mapear o comportamento da operação. Levante os seguintes números:

- **$U_{total}$ (Usuários Totais):** Quantos funcionários têm acesso ao sistema (ex: 50 analistas de suporte).
    
- **$U_{ativos}$ (Concorrência de Pico):** Quantos farão uma pergunta _exatamente ao mesmo tempo_. Em ferramentas de uso interno corporativo, a regra de ouro é calcular entre **10% a 20%** do total de usuários. (ex: 5 a 10 requisições simultâneas).
    
- **$T_{in}$ (Tokens de Entrada - O Peso do RAG):** Aqui está a maior armadilha. O usuário pode digitar apenas _"resuma o problema da carga 88492"_ (15 tokens), mas o seu orquestrador vai injetar o histórico do chat (500 tokens) + 3 PDFs de laudos recuperados do banco vetorial (3.000 tokens). Assuma um $T_{in}$ médio de 4.000 a 8.000 tokens.
    
- **$T_{out}$ (Tokens de Saída):** O tamanho médio da resposta da IA (ex: 300 tokens).
    

## 2. A Matemática "Debaixo do Capô": Pesos vs. KV Cache

Para saber a infraestrutura necessária, precisamos calcular a VRAM total exigida pela aplicação. A equação fundamental da inferência de LLMs é:

$$V_{total} = V_{pesos} + V_{kv\_cache}$$

### A. O Espaço dos Pesos ($V_{pesos}$)

É o espaço estático que o modelo ocupa na memória de vídeo apenas para existir. Se utilizarmos um modelo de 8 Bilhões de parâmetros (como o Llama 3 8B), e aplicarmos uma **quantização de 8-bits** (comprimindo a precisão matemática para economizar hardware), ele ocupará cerca de **8 GB a 9 GB de VRAM**.

- _Análise de Perda:_ A quantização reduz drasticamente o custo de hardware (um modelo sem quantização exigiria 16GB), mas ao reduzir a precisão dos cálculos de ponto flutuante, o modelo perde uma fração minúscula de sua capacidade de raciocínio lógico complexo. Para RAG corporativo, 8-bits ou 4-bits costumam ser mais do que suficientes.
    

### B. O Devorador de Memória: O KV Cache ($V_{kv\_cache}$)

Quando a IA começa a ler o prompt, ela gera matrizes de Atenção (Key-Value pairs) para lembrar do contexto. **Cada usuário conectado exige seu próprio KV Cache armazenado na GPU.** Em um modelo 8B padrão, manter 8.000 tokens de contexto na memória consome aproximadamente **1.5 GB de VRAM por usuário**.

Se você tiver 10 usuários simultâneos no pico ($U_{ativos} = 10$), cada um com um prompt de 8.000 tokens injetado pelo RAG:

$$V_{kv\_cache\_total} = 10 \times 1.5 \text{ GB} = 15 \text{ GB de VRAM}$$

### O Cálculo Final de VRAM

Neste cenário (Llama 3 8B + 10 usuários simultâneos com contextos grandes):

$$V_{total} = 8.5 \text{ GB (Pesos)} + 15 \text{ GB (KV Cache)} = 23.5 \text{ GB de VRAM}$$

---

## 3. O Fator do Motor de Inferência (Throughput)

Se você tentar rodar isso no motor básico do Ollama, assim que o 3º usuário fizer a requisição, o sistema vai colocar os outros em fila, e o usuário final ficará olhando para uma tela em branco por 40 segundos esperando o primeiro token aparecer (alto _Time to First Token - TTFT_).

A solução arquitetural obrigatória para ambientes multiusuário é utilizar motores como o **vLLM** ou o **TGI (Text Generation Inference)**, que utilizam uma técnica chamada **Continuous Batching** e **PagedAttention**.

- **Como funciona:** Em vez de processar o usuário A inteiro, depois o B, depois o C, o vLLM fatia o KV Cache em blocos de memória não contíguos (como o sistema de paginação de um SO tradicional). Ele junta os tokens gerados para todos os 10 usuários simultaneamente no mesmo ciclo da GPU.
    
- **Vantagem Operacional:** Você maximiza o rendimento (Throughput) do servidor. A GPU trabalha perto de 100% de utilização sem estourar a memória.
    
- **O Custo da Escolha:** Ao agrupar os processos de múltiplos usuários, a latência inter-token (o tempo entre uma palavra e outra aparecendo na tela) de cada indivíduo sobe ligeiramente. A resposta perde um pouco do aspecto "tempo real instantâneo", mas garante que ninguém tome erro de _Timeout_.
    

## 4. O Mapa de Hardware Prático para a PME

Traduzindo os números acima para o departamento de compras, com base em 5 a 15 usuários simultâneos (típico de PMEs com 50-100 funcionários usando a ferramenta esporadicamente):

1. **A Opção Custo-Benefício (Servidor Bare-Metal Próprio):**
    
    - **GPU:** 1x NVIDIA RTX 4090 (24GB de VRAM) ou 1x RTX 3090 (usada, também 24GB).
        
    - **Capacidade:** Roda com folga o Llama 3 8B (quantizado) suportando de 10 a 15 requisições pesadas de RAG simultaneamente usando vLLM.
        
    - **RAM/CPU de Host:** 64GB DDR5 e um processador moderno (Core i7/i9 ou Ryzen 9) para lidar com as buscas no banco de dados relacional e vetorização antes de enviar o prompt para a GPU.
        
2. **A Alternativa Apple Silicon (Mac Studio):**
    
    - **Hardware:** Mac Studio com chip M2/M3 Ultra e 64GB ou 128GB de Memória Unificada.
        
    - **O Paradigma:** Macs não têm VRAM dedicada; a memória RAM e de vídeo é a mesma. Isso permite rodar modelos gigantes ou suportar _muitos_ usuários simultâneos (KV Cache massivo).
        
    - **O Ponto de Atenção:** A largura de banda de memória do Mac (800 GB/s) é menor que a de uma RTX 4090 (1.000 GB/s). Portanto, o Mac suporta filas maiores de usuários sem dar erro de falta de memória (OOM), mas a velocidade de leitura da resposta (tokens por segundo) será visivelmente mais lenta.