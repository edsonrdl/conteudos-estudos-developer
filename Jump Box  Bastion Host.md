É um servidor que atua como um ponto de acesso seguro para outras instâncias privadas na sua rede. Em vez de abrir o acesso público a cada servidor, você se conecta primeiro ao Bastion Host (que é uma máquina com um IP público e segurança reforçada) e, a partir dele, acessa os servidores internos (que só têm IPs privados).

- **Finalidade:** O principal objetivo é reduzir a superfície de ataque da sua rede, já que apenas um ponto de entrada (o Bastion Host) precisa ser exposto à internet.
    
- **Como funciona:**
    
    1. Você acessa o Bastion Host via SSH (se for Linux) ou RDP (se for Windows).
        
    2. O Bastion Host possui as credenciais e as permissões de rede para se conectar às instâncias privadas da sua rede.
        
    3. A partir do Bastion Host, você pode executar comandos, transferir arquivos ou gerenciar as instâncias privadas de forma segura.
        

### Outras formas de acesso

Existem outras formas de acessar instâncias privadas sem um Bastion Host, mas elas são diferentes e têm nomes específicos:

- **VPN (Virtual Private Network):** Você cria um túnel criptografado entre a sua máquina e a sua rede na nuvem. Isso faz com que sua máquina se comporte como se estivesse fisicamente na mesma rede das instâncias privadas, permitindo o acesso direto a elas.
    
- **Acesso Direto com Ferramentas da AWS (ou outras nuvens):** Na AWS, por exemplo, o **AWS Systems Manager Session Manager** permite o acesso seguro a instâncias sem a necessidade de um Bastion Host, gerenciando a conexão por meio de agentes instalados nas próprias máquinas.
    

**Resumo:** O termo **"Jump Server"** é informal, mas bastante usado e se refere exatamente a essa técnica. No entanto, o nome técnico e mais preciso é **Bastion Host** ou **Jump Box**.