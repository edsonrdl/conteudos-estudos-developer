Uma Nuvem Virtual Privada (VPC) da Amazon é uma seção logicamente isolada da nuvem da Amazon Web Services (AWS), onde você pode iniciar recursos da AWS em uma rede virtual definida por você. Pense nela como seu próprio data center privado dentro da nuvem da AWS, oferecendo controle total sobre seu ambiente de rede.

Uma VPC permite que você:

- Defina um intervalo de endereços IP personalizado (bloco CIDR) para sua rede.
    
- Segmente sua rede em sub-redes.
    
- Configure tabelas de rotas para controlar o fluxo de tráfego.
    
- Use gateways de rede para se conectar à Internet, ao seu data center local ou a outras VPCs.
    

### Principais componentes de uma VPC

- **Sub-redes:** Uma sub-rede é um intervalo de endereços IP dentro da sua VPC. Você pode criar sub-redes diferentes para finalidades diferentes, como uma **sub-rede pública** para recursos que precisam ser acessíveis pela internet (como um servidor web) e uma **sub-rede privada** para recursos que não precisam (como um banco de dados).
    
- **Gateway de Internet:** Este componente permite a comunicação entre recursos na sua VPC e a internet. Uma VPC só pode ter um Gateway de Internet conectado a ela.
    
- **Gateway NAT:** Um gateway NAT (Network Address Translation) permite que instâncias em uma sub-rede privada se conectem à internet ou a outros serviços da AWS, mas impede conexões de entrada da internet. Esta é uma medida de segurança crucial.
    
- **Tabelas de Rota:** Essas tabelas contêm regras que determinam para onde o tráfego de rede das suas sub-redes é direcionado. Você pode ter uma tabela de rota principal para toda a VPC e tabelas de rota personalizadas para sub-redes específicas.
    
- **Grupos de Segurança:** Os grupos de segurança atuam como um firewall virtual para suas instâncias EC2, controlando o tráfego de entrada e saída no nível da instância. Eles têm estado, ou seja, se você permitir o tráfego de entrada, a resposta de saída será automaticamente permitida.
    
- **Listas de Controle de Acesso à Rede (NACLs):** As NACLs são uma camada opcional de segurança que atua como um firewall para sub-redes. Elas não têm estado, o que significa que você deve definir regras explicitamente para o tráfego de entrada e saída.
    

### Como funciona

Ao criar uma VPC, você especifica um bloco de Roteamento Interdomínio Sem Classe (CIDR). Este é o intervalo de endereços IP privados para sua VPC. Em seguida, você divide esse intervalo em blocos CIDR menores para criar sub-redes. Você pode alocar seus recursos, como uma instância EC2, nessas sub-redes.

Para permitir o acesso público, conecte um Gateway de Internet à VPC e configure uma tabela de rotas para direcionar o tráfego de internet da sua sub-rede pública para o Gateway de Internet. Para sub-redes privadas, você pode usar um Gateway NAT para permitir o tráfego de saída sem expor as instâncias à internet.

### Preços

A criação de uma VPC em si é gratuita. No entanto, você terá custos para determinados componentes e serviços opcionais utilizados na VPC, como:

- **Gateway NAT:** Cobrado por hora e pela quantidade de dados processados.
    
- **Peering de VPC:** a transferência de dados entre VPCs peering é cobrada.
    
- **Endereços IPv4 públicos:** você é cobrado por endereços IPv4 públicos, incluindo IPs elásticos, que estão anexados a uma instância em execução ou estão ociosos.
    
- **Transferência de dados:** taxas padrão de transferência de dados são aplicadas aos dados que saem da rede da AWS ou são transferidos entre diferentes regiões da AWS.


---
[Documentação aws vpc](https://aws.amazon.com/pt/blogs/awsforsap/vpc-subnet-zoning-patterns-for-sap-on-aws/)

![[8. DevOps e Gerenciamento de Infraestrutura/CLOUD COMPUTING/AWS (AMAZON WEB SERVICE)/img/vpc-1.png]]
