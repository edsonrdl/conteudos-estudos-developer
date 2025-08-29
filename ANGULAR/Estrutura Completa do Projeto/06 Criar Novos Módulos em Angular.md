# Como Criar Novos Módulos em Angular

## 1. Comandos Angular CLI para Criar Módulos

### Criar Módulo Simples
```bash
ng generate module features/users
# ou forma abreviada
ng g m features/users
```

### Criar Módulo com Roteamento
```bash
ng generate module features/users --routing
# ou
ng g m features/users --routing
```

### Criar Módulo Completo (com componente principal)
```bash
ng generate module features/users --routing --route users --module app
# Isso cria:
# - users.module.ts
# - users-routing.module.ts  
# - users.component.ts (componente principal)
# - Adiciona rota no app-routing.module.ts
```

### Criar Módulo com Lazy Loading
```bash
ng generate module features/products --routing --route products --module app
# Automaticamente configura lazy loading no app-routing
```

## 2. Estrutura de Comandos por Tipo de Módulo

### Para Módulo de Funcionalidade (Feature Module)
```bash
# Criar módulo base
ng g m features/orders --routing

# Criar páginas dentro do módulo
ng g c features/orders/pages/order-list --module=features/orders
ng g c features/orders/pages/order-detail --module=features/orders
ng g c features/orders/pages/order-create --module=features/orders

# Criar componentes reutilizáveis
ng g c features/orders/components/order-card --module=features/orders
ng g c features/orders/components/order-form --module=features/orders

# Criar serviços do módulo
ng g s features/orders/services/order
ng g s features/orders/services/order-facade

# Criar modelos/interfaces
ng g interface features/orders/models/order
ng g interface features/orders/models/order-filter

# Criar guards específicos
ng g guard features/orders/guards/order-access

# Criar resolvers
ng g resolver features/orders/resolvers/order
```

### Para Módulo Shared (Componentes Compartilhados)
```bash
# Criar módulo shared
ng g m shared

# Criar componentes compartilhados
ng g c shared/components/loading --module=shared --export
ng g c shared/components/modal --module=shared --export
ng g c shared/components/pagination --module=shared --export

# Criar pipes compartilhados
ng g pipe shared/pipes/currency-format --module=shared --export
ng g pipe shared/pipes/truncate --module=shared --export

# Criar diretivas compartilhadas
ng g directive shared/directives/click-outside --module=shared --export
ng g directive shared/directives/lazy-load --module=shared --export
```

### Para Módulo Core (Serviços Singleton)
```bash
# Criar módulo core (apenas se não existir)
ng g m core

# Criar serviços core
ng g s core/services/api
ng g s core/services/auth
ng g s core/services/notification
ng g s core/services/loading

# Criar interceptors
ng g interceptor core/interceptors/auth
ng g interceptor core/interceptors/error
ng g interceptor core/interceptors/loading

# Criar guards globais
ng g guard core/guards/auth
ng g guard core/guards/permission
```

## 3. Exemplos de Criação por Funcionalidade

### Módulo de Usuários Completo
```bash
# 1. Criar estrutura base
ng g m features/users --routing

# 2. Criar páginas principais  
ng g c features/users/pages/users-list --module=features/users
ng g c features/users/pages/user-detail --module=features/users
ng g c features/users/pages/user-create --module=features/users
ng g c features/users/pages/user-edit --module=features/users

# 3. Criar componentes reutilizáveis
ng g c features/users/components/user-card --module=features/users
ng g c features/users/components/user-form --module=features/users
ng g c features/users/components/user-avatar --module=features/users

# 4. Criar serviços
ng g s features/users/services/user-api
ng g s features/users/services/user-facade
ng g s features/users/services/user-cache

# 5. Criar modelos
ng g interface features/users/models/user
ng g interface features/users/models/user-filter
ng g interface features/users/models/user-permission

# 6. Criar guards específicos
ng g guard features/users/guards/user-management

# 7. Criar resolvers
ng g resolver features/users/resolvers/user
ng g resolver features/users/resolvers/user-list
```

### Módulo de Relatórios
```bash
# 1. Módulo base
ng g m features/reports --routing

# 2. Páginas de relatórios
ng g c features/reports/pages/sales-report --module=features/reports
ng g c features/reports/pages/inventory-report --module=features/reports
ng g c features/reports/pages/users-report --module=features/reports

# 3. Componentes de visualização
ng g c features/reports/components/chart-container --module=features/reports
ng g c features/reports/components/data-table --module=features/reports
ng g c features/reports/components/report-filters --module=features/reports

# 4. Serviços específicos
ng g s features/reports/services/reports-api
ng g s features/reports/services/chart-data
ng g s features/reports/services/export
```

### Módulo de Configurações
```bash
# 1. Módulo base
ng g m features/settings --routing

# 2. Páginas de configuração
ng g c features/settings/pages/general-settings --module=features/settings
ng g c features/settings/pages/user-preferences --module=features/settings
ng g c features/settings/pages/system-config --module=features/settings

# 3. Componentes específicos
ng g c features/settings/components/setting-item --module=features/settings
ng g c features/settings/components/theme-selector --module=features/settings

# 4. Serviços
ng g s features/settings/services/settings-api
ng g s features/settings/services/theme
```

## 4. Script Automatizado para Criar Módulo Completo

### create-feature-module.sh
```bash
#!/bin/bash

# Uso: ./create-feature-module.sh nome-do-modulo
MODULE_NAME=$1

if [ -z "$MODULE_NAME" ]; then
    echo "Uso: $0 <nome-do-modulo>"
    exit 1
fi

echo "Criando módulo: $MODULE_NAME"

# 1. Criar módulo com roteamento
ng g m features/$MODULE_NAME --routing

# 2. Criar estrutura de pastas e páginas
ng g c features/$MODULE_NAME/pages/$MODULE_NAME-list --module=features/$MODULE_NAME
ng g c features/$MODULE_NAME/pages/$MODULE_NAME-detail --module=features/$MODULE_NAME  
ng g c features/$MODULE_NAME/pages/$MODULE_NAME-create --module=features/$MODULE_NAME
ng g c features/$MODULE_NAME/pages/$MODULE_NAME-edit --module=features/$MODULE_NAME

# 3. Criar componentes reutilizáveis
ng g c features/$MODULE_NAME/components/$MODULE_NAME-card --module=features/$MODULE_NAME
ng g c features/$MODULE_NAME/components/$MODULE_NAME-form --module=features/$MODULE_NAME

# 4. Criar serviços
ng g s features/$MODULE_NAME/services/$MODULE_NAME-api
ng g s features/$MODULE_NAME/services/$MODULE_NAME-facade

# 5. Criar modelos
ng g interface features/$MODULE_NAME/models/$MODULE_NAME
ng g interface features/$MODULE_NAME/models/$MODULE_NAME-filter

# 6. Criar resolver
ng g resolver features/$MODULE_NAME/resolvers/$MODULE_NAME

echo "Módulo $MODULE_NAME criado com sucesso!"
echo "Não esqueça de:"
echo "1. Adicionar rota no app-routing.module.ts"
echo "2. Configurar o roteamento interno"
echo "3. Implementar os serviços"
```

### create-shared-component.sh
```bash
#!/bin/bash

# Uso: ./create-shared-component.sh nome-do-componente
COMPONENT_NAME=$1

if [ -z "$COMPONENT_NAME" ]; then
    echo "Uso: $0 <nome-do-componente>"
    exit 1
fi

echo "Criando componente compartilhado: $COMPONENT_NAME"

# Criar componente no shared module
ng g c shared/components/$COMPONENT_NAME --module=shared --export

echo "Componente compartilhado $COMPONENT_NAME criado!"
echo "Não esqueça de configurar os inputs/outputs necessários"
```

## 5. Comandos para Diferentes Tipos de Arquivos

### Interfaces e Modelos
```bash
# Interfaces
ng g interface shared/models/api-response
ng g interface features/products/models/product
ng g interface features/users/models/user-role

# Enums
ng g enum shared/enums/user-status
ng g enum features/orders/enums/order-status
```

### Serviços Especializados
```bash
# Serviços de API
ng g s core/services/base-api
ng g s features/products/services/products-api

# Serviços de Estado
ng g s features/products/services/products-state
ng g s features/users/services/users-facade

# Serviços Utilitários
ng g s shared/services/utils
ng g s shared/services/validation
```

### Guards e Resolvers
```bash
# Guards
ng g guard core/guards/auth --implements CanActivate
ng g guard features/admin/guards/admin-only --implements CanActivate
ng g guard shared/guards/unsaved-changes --implements CanDeactivate

# Resolvers
ng g resolver features/products/resolvers/product --resolver
ng g resolver features/users/resolvers/user-permissions --resolver
```

### Interceptors
```bash
# Interceptors globais
ng g interceptor core/interceptors/auth
ng g interceptor core/interceptors/error-handling
ng g interceptor core/interceptors/loading
ng g interceptor core/interceptors/cache
```

### Pipes e Diretivas
```bash
# Pipes
ng g pipe shared/pipes/safe-html --module=shared --export
ng g pipe shared/pipes/file-size --module=shared --export
ng g pipe features/products/pipes/price-format --module=features/products

# Diretivas
ng g directive shared/directives/auto-focus --module=shared --export
ng g directive shared/directives/permission --module=shared --export
ng g directive features/products/directives/product-highlight --module=features/products
```

## 6. Estrutura Final Após Criação
```
src/app/
├── core/
│   ├── services/
│   ├── guards/
│   ├── interceptors/
│   └── core.module.ts
├── shared/
│   ├── components/
│   ├── pipes/
│   ├── directives/
│   └── shared.module.ts
├── features/
│   ├── products/
│   │   ├── pages/
│   │   ├── components/
│   │   ├── services/
│   │   ├── models/
│   │   ├── guards/
│   │   ├── resolvers/
│   │   ├── products.module.ts
│   │   └── products-routing.module.ts
│   ├── users/
│   │   ├── pages/
│   │   ├── components/
│   │   ├── services/
│   │   ├── models/
│   │   ├── users.module.ts
│   │   └── users-routing.module.ts
│   └── orders/
│       ├── pages/
│       ├── components/
│       ├── services/
│       ├── orders.module.ts
│       └── orders-routing.module.ts
├── layout/
│   ├── header/
│   ├── sidebar/
│   └── main-layout/
├── app-routing.module.ts
└── app.module.ts
```

## 7. Comandos Úteis para Manutenção

### Verificar estrutura criada
```bash
# Ver estrutura de arquivos
tree src/app/features/

# Ver módulos registrados
ng build --stats-json
npx webpack-bundle-analyzer dist/stats.json
```

### Remover módulos (cuidado!)
```bash
# Angular CLI não tem comando para remover
# Você deve remover manualmente:
# 1. Deletar pasta do módulo
rm -rf src/app/features/module-name

# 2. Remover referências do app-routing.module.ts
# 3. Remover imports se houver
```

### Gerar módulo com configurações específicas
```bash
# Módulo com lazy loading automático
ng g m features/dashboard --routing --route dashboard --module app

# Módulo compartilhado sem roteamento  
ng g m shared

# Módulo core (singleton)
ng g m core --skip-tests
```