## 1. O que é Event-Driven

No modelo orientado a eventos, o fluxo do programa é determinado por **eventos** — ocorrências internas ou externas — e por **handlers** (manipuladores) que reagem a esses eventos.

- **Evento**: qualquer ação detectável, como clique do usuário, mensagem recebida, mudança de estado.
- **Handler**: função ou método que “ouve” (listener) um tipo de evento e executa uma rotina quando ele ocorre.

> Analogia: é como instalar vários sensores num prédio (movimento, temperatura, porta) e, quando um sensor dispara, um alarme ou robô específico entra em ação.

---

## 2. Características principais

1. **Desacoplamento**
    - Produtores de eventos não conhecem quem vai tratá-los; comunicam-se via canal de eventos.
2. **Assincronia**
    - Handlers podem rodar fora da sequência principal, em paralelo ou sequencialmente conforme implementação.
3. **Listeners e Publishers**
    - Componentes se **registram** para ouvir certos eventos (subscribe) e **publicam** eventos quando algo acontece (publish).
4. **Loop de eventos**
    - Um mecanismo central (`event loop`) coleta eventos e despacha aos handlers adequados.

---

## 3. Exemplos

### JavaScript (navegador)

```jsx
// Seleciona um botão na página
const botao = document.getElementById('meuBotao');

// Registra um listener para o evento de clique
botao.addEventListener('click', () => {
  console.log('O usuário clicou no botão!');
});

```

- O navegador mantém um **event loop** que aguarda cliques.
- Quando o usuário clica, o callback fornecido é executado.

### Node.js (servidor)

```jsx
const http = require('http');

const server = http.createServer((req, res) => {
  // Esse callback é um handler para o evento de requisição HTTP
  res.end('Olá, mundo!');
});

server.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});

```

- O Node.js usa um único thread com um **loop** que dispara handlers para cada requisição.

### Java Swing (GUI)

```java

JButton botao = new JButton("Clique");
botao.addActionListener(e -> {
    // Handler para o evento de ação (clique)
    System.out.println("Botão pressionado");
});

```

- A UI registra _listeners_ que respondem a eventos de interface.

---

## 4. Vantagens

1. **Escalabilidade**
    - Boa para aplicações que lidam com muitas entradas assíncronas (I/O, mensagens).
2. **Desacoplamento de componentes**
    - Produtor e consumidor de eventos ficam independentes; facilita manutenção.
3. **Responsividade**
    - Interfaces gráficas e servidores podem continuar funcionando enquanto aguardam eventos.
4. **Flexibilidade**
    - Novos handlers podem ser adicionados ou removidos em tempo de execução.

---

## 5. Desvantagens

1. **Complexidade no fluxo**
    - Debug e rastreamento ficam mais difíceis: não há sequência linear de execução.
2. **Gerenciamento de estado**
    - Compartilhar dados entre handlers exige cuidado (concorrência, condições de corrida).
3. **Sobrecarga de gerenciamento**
    - Sistemas com muitos tipos de eventos e listeners podem se tornar difíceis de configurar e entender.
4. **Tratamento de erros**
    - Exceções em handlers podem não propagar de forma clara, exigindo estratégias específicas de captura e logging.

---

## 6. Quando usar

- **Interfaces gráficas** e **aplicações desktop**, onde a interação do usuário define o fluxo.
- **Servidores de alta concorrência** (por exemplo, gateways HTTP, sistemas de mensageria).
- **IoT e automação industrial**, com muitos dispositivos gerando eventos.
- **Arquiteturas de microsserviços** que comunicam por filas de mensagens (Kafka, RabbitMQ).