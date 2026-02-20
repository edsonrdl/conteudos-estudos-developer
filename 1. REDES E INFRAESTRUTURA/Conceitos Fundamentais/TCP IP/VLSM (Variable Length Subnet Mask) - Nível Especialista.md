Imagine que a **ASI** tem um bloco de IPs `/24` (254 IPs).

- O setor de **Dev** precisa de 100 IPs.
    
- O setor de **RH** precisa de 20 IPs.
    
- O link com o **Banco** precisa de 2 IPs.
    

Se você usar `/24` para todo mundo, você desperdiça milhares de IPs. Com o **VLSM**, você "quebra" o `/24` em pedaços menores:

- Dá um `/25` (126 IPs) para o Dev.
    
- Dá um `/27` (30 IPs) para o RH.
    
- Dá um `/30` (2 IPs) para o Banco.
    

Isso é o que você faz dentro da **AWS VPC** quando divide seu CIDR principal em subnets para diferentes Availability Zones.