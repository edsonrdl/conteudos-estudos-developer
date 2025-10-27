**Contexto Principal:** Ambientes onde a **latência e o desempenho** são as métricas mais críticas, como sistemas de negociação de alta frequência ou APIs em tempo real.

- **Como Funciona:** Este algoritmo direciona o tráfego para o servidor que tem o **menor tempo médio de resposta** E o menor número de conexões ativas, tudo isso ponderado pelo seu peso atribuído.
    
- **Vantagem na Migração:** É ideal para uma migração onde você espera que os novos servidores (que geralmente têm pesos maiores) sejam **significativamente mais rápidos**. Ele otimiza a experiência do usuário direcionando as requisições para o servidor que responde mais rapidamente no momento, enquanto ainda respeita a proporção de capacidade.
    
- **Desvantagem:** É o algoritmo mais complexo. Se o monitoramento do tempo de resposta não for preciso ou se houver picos momentâneos de latência, o balanceador pode tomar decisões subótimas.