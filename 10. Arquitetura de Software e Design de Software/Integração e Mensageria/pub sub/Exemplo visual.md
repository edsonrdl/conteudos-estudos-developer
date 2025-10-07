Imagine um sistema de e-commerce:

<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pub/Sub - Demonstração Interativa</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
            color: #333;
        }
        
        .container {
            max-width: 1400px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
        }
        
        h1 {
            text-align: center;
            color: #764ba2;
            margin-bottom: 10px;
            font-size: 2.5em;
        }
        
        .subtitle {
            text-align: center;
            color: #666;
            margin-bottom: 30px;
            font-size: 1.1em;
        }
        
        .demo-container {
            display: grid;
            grid-template-columns: 1fr 2fr 1fr;
            gap: 30px;
            margin-bottom: 30px;
        }
        
        .section {
            background: white;
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
        }
        
        .publishers, .subscribers {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        
        .publisher, .subscriber {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 15px;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }
        
        .publisher:hover, .subscriber:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(118, 75, 162, 0.4);
        }
        
        .publisher::before, .subscriber::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: rgba(255, 255, 255, 0.2);
            transition: left 0.5s ease;
        }
        
        .publisher:hover::before, .subscriber:hover::before {
            left: 100%;
        }
        
        .publisher h3, .subscriber h3 {
            margin-bottom: 5px;
            font-size: 1.1em;
        }
        
        .publisher small, .subscriber small {
            opacity: 0.9;
            font-size: 0.85em;
        }
        
        .broker {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }
        
        .topic {
            background: #f8f9fa;
            border: 2px solid #e9ecef;
            border-radius: 10px;
            padding: 15px;
            position: relative;
        }
        
        .topic-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
        }
        
        .topic-name {
            font-weight: bold;
            color: #495057;
            font-size: 1.1em;
        }
        
        .topic-count {
            background: #764ba2;
            color: white;
            padding: 3px 8px;
            border-radius: 15px;
            font-size: 0.85em;
        }
        
        .messages {
            max-height: 150px;
            overflow-y: auto;
            margin-top: 10px;
        }
        
        .message {
            background: white;
            border-left: 3px solid #667eea;
            padding: 8px;
            margin-bottom: 5px;
            border-radius: 5px;
            font-size: 0.9em;
            animation: slideIn 0.3s ease;
        }
        
        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateX(-20px);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }
        
        .message-flow {
            position: absolute;
            background: #667eea;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            opacity: 0;
            pointer-events: none;
            z-index: 1000;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 12px;
        }
        
        .message-flow.animate {
            animation: flow 1.5s ease;
        }
        
        @keyframes flow {
            0% {
                opacity: 0;
                transform: scale(0);
            }
            20% {
                opacity: 1;
                transform: scale(1);
            }
            80% {
                opacity: 1;
                transform: scale(1);
            }
            100% {
                opacity: 0;
                transform: scale(0);
            }
        }
        
        .controls {
            display: flex;
            gap: 10px;
            justify-content: center;
            margin-bottom: 20px;
        }
        
        .btn {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 12px 25px;
            border-radius: 25px;
            cursor: pointer;
            font-size: 1em;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(118, 75, 162, 0.3);
        }
        
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(118, 75, 162, 0.4);
        }
        
        .btn:active {
            transform: translateY(0);
        }
        
        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-top: 20px;
        }
        
        .stat-card {
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            padding: 15px;
            border-radius: 10px;
            text-align: center;
        }
        
        .stat-value {
            font-size: 2em;
            font-weight: bold;
            color: #764ba2;
        }
        
        .stat-label {
            color: #666;
            margin-top: 5px;
        }
        
        .legend {
            background: #f8f9fa;
            padding: 15px;
            border-radius: 10px;
            margin-top: 20px;
        }
        
        .legend h3 {
            color: #495057;
            margin-bottom: 10px;
        }
        
        .legend-item {
            display: flex;
            align-items: center;
            margin-bottom: 8px;
            font-size: 0.95em;
        }
        
        .legend-color {
            width: 20px;
            height: 20px;
            border-radius: 50%;
            margin-right: 10px;
        }
        
        .subscriber-log {
            background: #f8f9fa;
            padding: 10px;
            border-radius: 8px;
            margin-top: 10px;
            max-height: 100px;
            overflow-y: auto;
            font-size: 0.85em;
        }
        
        .log-entry {
            padding: 3px 0;
            color: #666;
            border-bottom: 1px solid #e9ecef;
        }
        
        .log-entry:last-child {
            border-bottom: none;
        }
        
        @media (max-width: 768px) {
            .demo-container {
                grid-template-columns: 1fr;
            }
            
            h1 {
                font-size: 1.8em;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🚀 Pub/Sub Pattern</h1>
        <p class="subtitle">Clique nos publishers para enviar mensagens e veja como são distribuídas</p>
        
        <div class="controls">
            <button class="btn" onclick="clearAll()">🔄 Limpar Tudo</button>
            <button class="btn" onclick="simulateRealTime()">▶️ Simular Tempo Real</button>
            <button class="btn" onclick="toggleSubscriber('email')">📧 Toggle Email</button>
            <button class="btn" onclick="toggleSubscriber('analytics')">📊 Toggle Analytics</button>
        </div>
        
        <div class="demo-container">
            <!-- Publishers -->
            <div class="section">
                <h2 style="color: #667eea; margin-bottom: 15px;">📤 Publishers</h2>
                <div class="publishers">
                    <div class="publisher" onclick="publish('ecommerce', 'orders', 'Novo pedido #1234')">
                        <h3>🛒 E-commerce API</h3>
                        <small>Clique para publicar pedido</small>
                    </div>
                    <div class="publisher" onclick="publish('payment', 'orders', 'Pagamento confirmado')">
                        <h3>💳 Gateway Pagamento</h3>
                        <small>Clique para confirmar pagamento</small>
                    </div>
                    <div class="publisher" onclick="publish('inventory', 'inventory', 'Estoque atualizado')">
                        <h3>📦 Sistema Inventário</h3>
                        <small>Clique para atualizar estoque</small>
                    </div>
                    <div class="publisher" onclick="publish('user', 'users', 'Novo usuário cadastrado')">
                        <h3>👤 User Service</h3>
                        <small>Clique para novo cadastro</small>
                    </div>
                </div>
            </div>
            
            <!-- Message Broker -->
            <div class="section">
                <h2 style="color: #764ba2; margin-bottom: 15px; text-align: center;">🔄 Message Broker (Tópicos)</h2>
                <div class="broker">
                    <div class="topic">
                        <div class="topic-header">
                            <span class="topic-name">📋 orders</span>
                            <span class="topic-count" id="orders-count">0 msgs</span>
                        </div>
                        <div class="messages" id="orders-messages"></div>
                    </div>
                    
                    <div class="topic">
                        <div class="topic-header">
                            <span class="topic-name">📦 inventory</span>
                            <span class="topic-count" id="inventory-count">0 msgs</span>
                        </div>
                        <div class="messages" id="inventory-messages"></div>
                    </div>
                    
                    <div class="topic">
                        <div class="topic-header">
                            <span class="topic-name">👥 users</span>
                            <span class="topic-count" id="users-count">0 msgs</span>
                        </div>
                        <div class="messages" id="users-messages"></div>
                    </div>
                </div>
            </div>
            
            <!-- Subscribers -->
            <div class="section">
                <h2 style="color: #667eea; margin-bottom: 15px;">📥 Subscribers</h2>
                <div class="subscribers">
                    <div class="subscriber" id="sub-email">
                        <h3>📧 Email Service</h3>
                        <small>Inscrito em: orders, users</small>
                        <div class="subscriber-log" id="log-email"></div>
                    </div>
                    <div class="subscriber" id="sub-analytics">
                        <h3>📊 Analytics</h3>
                        <small>Inscrito em: todos os tópicos</small>
                        <div class="subscriber-log" id="log-analytics"></div>
                    </div>
                    <div class="subscriber" id="sub-warehouse">
                        <h3>🏭 Warehouse</h3>
                        <small>Inscrito em: orders, inventory</small>
                        <div class="subscriber-log" id="log-warehouse"></div>
                    </div>
                    <div class="subscriber" id="sub-crm">
                        <h3>💼 CRM System</h3>
                        <small>Inscrito em: users</small>
                        <div class="subscriber-log" id="log-crm"></div>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="stats">
            <div class="stat-card">
                <div class="stat-value" id="total-published">0</div>
                <div class="stat-label">Mensagens Publicadas</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="total-delivered">0</div>
                <div class="stat-label">Mensagens Entregues</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="active-subscribers">4</div>
                <div class="stat-label">Subscribers Ativos</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="total-topics">3</div>
                <div class="stat-label">Tópicos</div>
            </div>
        </div>
        
        <div class="legend">
            <h3>📚 Como funciona o Pub/Sub:</h3>
            <div class="legend-item">
                <div class="legend-color" style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);"></div>
                <span><strong>Publishers:</strong> Enviam mensagens para tópicos sem saber quem vai receber</span>
            </div>
            <div class="legend-item">
                <div class="legend-color" style="background: #f8f9fa; border: 2px solid #e9ecef;"></div>
                <span><strong>Tópicos:</strong> Categorizam e distribuem mensagens para os interessados</span>
            </div>
            <div class="legend-item">
                <div class="legend-color" style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);"></div>
                <span><strong>Subscribers:</strong> Recebem apenas mensagens dos tópicos que assinam</span>
            </div>
            <div class="legend-item" style="margin-top: 10px; padding-top: 10px; border-top: 1px solid #e9ecef;">
                <span>💡 <strong>Vantagem:</strong> Publishers e Subscribers são completamente desacoplados - podem ser adicionados ou removidos sem afetar uns aos outros!</span>
            </div>
        </div>
    </div>
    
    <div class="message-flow" id="message-flow">📨</div>
    
    <script>
        // Estado da aplicação
        let stats = {
            published: 0,
            delivered: 0,
            activeSubscribers: 4
        };
        
        let messageCount = {
            orders: 0,
            inventory: 0,
            users: 0
        };
        
        // Configuração de inscrições
        const subscriptions = {
            email: {
                active: true,
                topics: ['orders', 'users'],
                icon: '📧'
            },
            analytics: {
                active: true,
                topics: ['orders', 'inventory', 'users'],
                icon: '📊'
            },
            warehouse: {
                active: true,
                topics: ['orders', 'inventory'],
                icon: '🏭'
            },
            crm: {
                active: true,
                topics: ['users'],
                icon: '💼'
            }
        };
        
        // Publicar mensagem
        function publish(publisher, topic, message) {
            const timestamp = new Date().toLocaleTimeString('pt-BR');
            const fullMessage = `[${timestamp}] ${publisher}: ${message}`;
            
            // Adicionar mensagem ao tópico
            const topicElement = document.getElementById(`${topic}-messages`);
            const messageElement = document.createElement('div');
            messageElement.className = 'message';
            messageElement.textContent = fullMessage;
            topicElement.insertBefore(messageElement, topicElement.firstChild);
            
            // Limitar mensagens visíveis
            while (topicElement.children.length > 5) {
                topicElement.removeChild(topicElement.lastChild);
            }
            
            // Atualizar contador do tópico
            messageCount[topic]++;
            document.getElementById(`${topic}-count`).textContent = `${messageCount[topic]} msgs`;
            
            // Animação de fluxo
            animateMessageFlow(publisher, topic);
            
            // Distribuir para subscribers
            setTimeout(() => {
                distributeMessage(topic, fullMessage);
            }, 500);
            
            // Atualizar estatísticas
            stats.published++;
            updateStats();
        }
        
        // Distribuir mensagem para subscribers
        function distributeMessage(topic, message) {
            Object.entries(subscriptions).forEach(([subName, subConfig]) => {
                if (subConfig.active && subConfig.topics.includes(topic)) {
                    const logElement = document.getElementById(`log-${subName}`);
                    const logEntry = document.createElement('div');
                    logEntry.className = 'log-entry';
                    logEntry.textContent = `${subConfig.icon} ${message}`;
                    logElement.insertBefore(logEntry, logElement.firstChild);
                    
                    // Limitar logs
                    while (logElement.children.length > 3) {
                        logElement.removeChild(logElement.lastChild);
                    }
                    
                    // Destacar subscriber
                    const subElement = document.getElementById(`sub-${subName}`);
                    subElement.style.animation = 'pulse 0.5s';
                    setTimeout(() => {
                        subElement.style.animation = '';
                    }, 500);
                    
                    stats.delivered++;
                    updateStats();
                }
            });
        }
        
        // Animação de fluxo de mensagem
        function animateMessageFlow(publisher, topic) {
            const flow = document.getElementById('message-flow');
            flow.classList.add('animate');
            setTimeout(() => {
                flow.classList.remove('animate');
            }, 1500);
        }
        
        // Toggle subscriber
        function toggleSubscriber(subName) {
            subscriptions[subName].active = !subscriptions[subName].active;
            const subElement = document.getElementById(`sub-${subName}`);
            
            if (subscriptions[subName].active) {
                subElement.style.opacity = '1';
                stats.activeSubscribers++;
            } else {
                subElement.style.opacity = '0.5';
                stats.activeSubscribers--;
            }
            
            updateStats();
        }
        
        // Limpar tudo
        function clearAll() {
            ['orders', 'inventory', 'users'].forEach(topic => {
                document.getElementById(`${topic}-messages`).innerHTML = '';
                messageCount[topic] = 0;
                document.getElementById(`${topic}-count`).textContent = '0 msgs';
            });
            
            ['email', 'analytics', 'warehouse', 'crm'].forEach(sub => {
                document.getElementById(`log-${sub}`).innerHTML = '';
            });
            
            stats.published = 0;
            stats.delivered = 0;
            updateStats();
        }
        
        // Simular tempo real
        function simulateRealTime() {
            const publishers = [
                { name: 'ecommerce', topic: 'orders', message: 'Novo pedido' },
                { name: 'payment', topic: 'orders', message: 'Pagamento processado' },
                { name: 'inventory', topic: 'inventory', message: 'Estoque baixo' },
                { name: 'user', topic: 'users', message: 'Login realizado' },
                { name: 'ecommerce', topic: 'orders', message: 'Carrinho abandonado' },
                { name: 'inventory', topic: 'inventory', message: 'Reabastecimento' },
                { name: 'user', topic: 'users', message: 'Perfil atualizado' }
            ];
            
            let index = 0;
            const interval = setInterval(() => {
                if (index >= publishers.length) {
                    clearInterval(interval);
                    return;
                }
                
                const pub = publishers[index];
                publish(pub.name, pub.topic, `${pub.message} #${Math.floor(Math.random() * 9999)}`);
                index++;
            }, 1500);
        }
        
        // Atualizar estatísticas
        function updateStats() {
            document.getElementById('total-published').textContent = stats.published;
            document.getElementById('total-delivered').textContent = stats.delivered;
            document.getElementById('active-subscribers').textContent = stats.activeSubscribers;
        }
        
        // Adicionar animação de pulse
        const style = document.createElement('style');
        style.textContent = `
            @keyframes pulse {
                0% { transform: scale(1); }
                50% { transform: scale(1.05); box-shadow: 0 5px 25px rgba(118, 75, 162, 0.6); }
                100% { transform: scale(1); }
            }
        `;
        document.head.appendChild(style);
        
        // Mensagem inicial
        setTimeout(() => {
            publish('system', 'orders', 'Sistema inicializado');
        }, 1000);
    </script>
</body>
</html>