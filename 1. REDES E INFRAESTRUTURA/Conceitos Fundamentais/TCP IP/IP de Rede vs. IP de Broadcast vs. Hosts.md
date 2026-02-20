Em qualquer sub-rede que você calcular, existem dois endereços que você **nunca** pode atribuir a uma instância EC2 ou um PC:

1. **Endereço de Rede:** É o primeiro IP da faixa (ex: `192.168.1.0`). Identifica a rede inteira.
    
2. **Endereço de Broadcast:** É o último IP da faixa (ex: `192.168.1.255`). Serve para enviar um pacote para todos os dispositivos daquela rede ao mesmo tempo.
    
3. **Hosts Utilizáveis:** Todos os números que sobraram entre o primeiro e o último.
    

**Cálculo de quantidade de máquinas:**

$$Fórmula: 2^{(bits\_restantes)} - 2$$

Se você tem um `/24`, sobraram 8 bits para hosts.

$2^8 = 256$. $256 - 2 = 254$máquinas disponíveis.