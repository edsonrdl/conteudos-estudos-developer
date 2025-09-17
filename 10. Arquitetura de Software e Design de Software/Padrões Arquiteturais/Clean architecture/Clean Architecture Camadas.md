![[10. Arquitetura de Software e Design de Software/img/clean-architecture.png]]
## **Camada Central - Entities (Entidades)**

Esta é o núcleo da aplicação, contendo:

- **Regras de negócio fundamentais**: Lógicas que existem independentemente da aplicação
- **Objetos de domínio**: Representam conceitos centrais do negócio
- **Políticas críticas**: Regras que raramente mudam e são essenciais ao sistema
- **Exemplo**: Uma entidade "Cliente" com regras como validação de CPF, cálculos de crédito

## **Segunda Camada - Use Cases (Casos de Uso)**

Contém a lógica específica da aplicação:

- **Regras de negócio da aplicação**: Como os dados fluem entre entidades
- **Orquestração**: Coordena chamadas para entidades e interfaces externas
- **Políticas específicas**: Regras que podem mudar conforme a aplicação evolui
- **Exemplo**: "Processar Pedido" - valida cliente, verifica estoque, calcula preço

## **Terceira Camada - Interface Adapters**

Converte dados entre formatos:

- **Controllers**: Recebem requisições e as convertem para o formato dos Use Cases
- **Presenters**: Formatam dados dos Use Cases para exibição
- **Gateways**: Abstraem acesso a dados externos (bancos, APIs)
- **Exemplo**: Controller REST que recebe JSON e converte para objetos de domínio

## **Camada Externa - Frameworks & Drivers**

Ferramentas e tecnologias específicas:

- **Web**: Servidores web, frameworks HTTP
- **UI**: Interfaces gráficas, páginas web
- **DB**: Bancos de dados específicos (MySQL, MongoDB)
- **External Interfaces**: APIs externas, serviços de terceiros
- **Devices**: Hardware, sensores, impressoras

## **Componentes Externos Detalhados:**

### **Enterprise Business Rules**

- Regras que transcendem uma única aplicação
- Políticas corporativas gerais
- Validações de domínio universais

### **Application Business Rules**

- Regras específicas desta aplicação
- Fluxos de trabalho particulares
- Casos de uso únicos do sistema

### **Interface Adapters**

- **Controllers**: Controlam fluxo de entrada
- **Presenters**: Formatam saída de dados
- **Gateways**: Isolam acesso a recursos externos

### **Frameworks & Drivers**

- Tecnologias substituíveis
- Bibliotecas externas
- Ferramentas de infraestrutura

## **Fluxo de Controle (Diagrama Direito)**

1. **Controller** recebe requisição externa
2. **Use Case Input Port** define interface de entrada
3. **Use Case Interactor** executa a lógica de negócio
4. **Use Case Output Port** define interface de saída
5. **Presenter** formata dados para apresentação

## **Princípios Fundamentais:**

- **Dependência unidirecional**: Camadas internas nunca dependem das externas
- **Independência de frameworks**: Núcleo não conhece tecnologias específicas
- **Testabilidade**: Camadas internas podem ser testadas isoladamente
- **Flexibilidade**: Tecnologias externas podem ser substituídas sem afetar o núcleo

Essa arquitetura garante que as regras de negócio permaneçam protegidas e independentes de mudanças tecnológicas, facilitando manutenção e evolução do sistema.