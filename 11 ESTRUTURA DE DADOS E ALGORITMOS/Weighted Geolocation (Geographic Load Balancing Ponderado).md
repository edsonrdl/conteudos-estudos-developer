**Contexto Principal:** Migração em ambientes distribuídos globalmente (Multi-Região ou CDN).

- **Como Funciona:** Combina o peso com a localização geográfica do usuário. O tráfego é primeiramente direcionado para o servidor mais próximo do usuário (menor latência), e dentro dessa região, o algoritmo ponderado (geralmente WRR ou WLC) decide qual servidor local receberá a requisição.
    
- **Vantagem na Migração:** Permite uma migração por região. Você pode aumentar gradualmente o peso dos novos servidores (destino) **apenas em uma região específica** antes de replicar a mudança para o resto do mundo. Isso minimiza o impacto global de qualquer falha de migração.