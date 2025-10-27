**Contexto Principal:** Ambientes onde as conexões têm **duração variada** (por exemplo, sessões de e-commerce, streaming, ou serviços de API com conexões persistentes).

- **Como Funciona:** Em vez de apenas contar o número de requisições (como o WRR), ele monitora o **número de conexões ativas** em cada servidor. O tráfego é enviado para o servidor que tiver a **menor proporção de conexões ativas / peso**.
    
- **Vantagem na Migração:** É mais "inteligente" do que o Round-Robin. Se o seu novo servidor for mais potente (peso maior), mas estiver sobrecarregado por poucas conexões de longa duração, ele evitará enviá-lo para lá, garantindo que o tráfego seja direcionado para onde a capacidade real está disponível no momento.
    
- **Desvantagem:** Requer que o balanceador de carga monitore ativamente o estado das conexões dos servidores, o que é mais complexo que o WRR.