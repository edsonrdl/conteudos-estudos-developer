A infraestrutura da Amazon Web Services (AWS) vai muito além das suas grandes Regiões. Para atender a necessidades específicas de baixa latência e requisitos de residência de dados, a AWS desenvolveu soluções que estendem sua infraestrutura para mais perto dos clientes.

---

### **Availability Zones (AZs)**

**Availability Zones** (Zonas de Disponibilidade) são a espinha dorsal da alta disponibilidade na AWS. Uma AZ é um ou mais data centers, isolados de falhas de energia, conectividade e segurança de outras AZs dentro da mesma **Região**. A distância física entre as AZs garante que um desastre natural ou falha de infraestrutura em uma delas não afete as outras. Ao distribuir seus aplicativos e dados entre várias AZs, você pode criar arquiteturas de alta disponibilidade e tolerância a falhas, onde se uma AZ ficar indisponível, o tráfego é automaticamente direcionado para outra.

---

### **Local Zones**

**Local Zones** (Zonas Locais) são uma extensão das Regiões AWS para áreas metropolitanas e industriais. O objetivo é levar serviços de computação, armazenamento e outros serviços AWS comumente usados mais perto de usuários finais e cargas de trabalho que exigem latência ultrabaixa. Diferente das AZs, que estão dentro de uma Região, as Local Zones são geograficamente mais distantes. Elas são ideais para aplicações como jogos online, transmissão de vídeos ao vivo, automação industrial e criação de conteúdo, onde cada milissegundo conta. O gerenciamento e a conectividade de uma Local Zone são feitos através da sua Região-mãe.

---

### **Wavelength Zones**

Embora não esteja explicitamente na sua lista, as **Wavelength Zones** merecem destaque por serem uma evolução no conceito de proximidade. Elas incorporam serviços AWS, como Amazon EC2 e EBS, na borda da rede 5G das operadoras de telefonia móvel. Isso permite que desenvolvedores criem aplicações que se beneficiam de uma latência de um dígito em milissegundos para dispositivos conectados à rede 5G, como veículos autônomos, realidade aumentada/virtual e sistemas de controle em tempo real.

---

### **Outposts Family**

A família **AWS Outposts** é a solução para "simular a nuvem em on-premise", mas de uma forma muito mais integrada e gerenciada do que uma simulação. O AWS Outposts é um serviço totalmente gerenciado que leva a infraestrutura e os serviços da AWS diretamente para o seu data center, escritório ou qualquer local físico. Existem duas opções principais:

- **Outposts Rack (Rack):** Uma solução de rack de 42U que a AWS entrega e instala no seu local. Ele contém servidores, armazenamento e equipamentos de rede da AWS, rodando serviços como EC2 e EBS. Você gerencia a infraestrutura usando as mesmas APIs, ferramentas e painel de controle que usaria na nuvem pública da AWS.
    
- **Outposts Servers (Servidores):** Dispositivos compactos de 1U ou 2U para locais com restrição de espaço, como lojas de varejo, filiais ou fábricas. Eles funcionam da mesma forma que os racks, mas em uma escala menor.
    

O Outposts é perfeito para casos de uso que exigem processamento local, baixa latência para sistemas on-premise, ou requisitos de residência de dados. Ele permite que você execute parte da sua aplicação localmente, mantendo a consistência da experiência da AWS, enquanto se conecta à Região AWS principal para serviços que não precisam de baixa latência.

Em resumo, a AWS oferece uma gama de opções que expandem sua nuvem principal para a borda e para as instalações do cliente. Isso permite que empresas atendam a requisitos de latência, segurança e residência de dados sem sacrificar a flexibilidade e escalabilidade da nuvem.