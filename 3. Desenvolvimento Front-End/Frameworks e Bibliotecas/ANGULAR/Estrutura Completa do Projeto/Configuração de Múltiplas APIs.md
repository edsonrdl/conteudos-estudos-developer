
## 1. Estrutura de Pastas para Múltiplas APIs

```
my-enterprise-app/
├── src/
│   ├── app/
│   │   ├── core/
│   │   │   ├── services/
│   │   │   │   ├── base-api.service.ts
│   │   │   │   ├── products-api.service.ts
│   │   │   │   ├── users-api.service.ts
│   │   │   │   ├── orders-api.service.ts
│   │   │   │   └── analytics-api.service.ts
│   │   │   ├── config/
│   │   │   │   ├── api-endpoints.config.ts
│   │   │   │   └── environment-config.service.ts
│   │   │   ├── interceptors/
│   │   │   │   ├── multi-api-auth.interceptor.ts
│   │   │   │   └── api-selector.interceptor.ts
│   │   │   └── core.module.ts
│   │   │
│   │   ├── features/
│   │   │   ├── products/
│   │   │   │   ├── products.module.ts
│   │   │   │   ├── products-routing.module.ts
│   │   │   │   ├── pages/
│   │   │   │   ├── components/
│   │   │   │   └── services/
│   │   │   │
│   │   │   ├── users/
│   │   │   │   ├── users.module.ts
│   │   │   │   ├── users-routing.module.ts
│   │   │   │   ├── pages/
│   │   │   │   ├── components/
│   │   │   │   └── services/
│   │   │   │
│   │   │   ├── orders/
│   │   │   │   ├── orders.module.ts
│   │   │   │   ├── orders-routing.module.ts
│   │   │   │   └── pages/
│   │   │   │
│   │   │   └── analytics/
│   │   │       ├── analytics.module.ts
│   │   │       ├── analytics-routing.module.ts
│   │   │       └── pages/
│   │   │
│   │   ├── layout/
│   │   │   ├── main-layout/
│   │   │   └── admin-layout/
│   │   │
│   │   └── app-routing.module.ts
│   └── environments/
│       ├── environment.ts
│       └── environment.prod.ts
```


### core/config/api-endpoints.config.ts
```typescript
import { InjectionToken } from '@angular/core';

export interface ApiConfig {
  baseUrl: string;
  version: string;
  timeout: number;
  retryAttempts: number;
}

export interface ApiEndpointsConfig {
  products: ApiConfig;
  users: ApiConfig;
  orders: ApiConfig;
  analytics: ApiConfig;
  payments: ApiConfig;
}

export const API_ENDPOINTS_CONFIG = new InjectionToken<ApiEndpointsConfig>('api.endpoints.config');

// Factory function para configuração
export function createApiEndpointsConfig(): ApiEndpointsConfig {
  return {
    products: {
      baseUrl: 'https://products-api.company.com',
      version: 'v1',
      timeout: 30000,
      retryAttempts: 2
    },
    users: {
      baseUrl: 'https://users-api.company.com', 
      version: 'v2',
      timeout: 15000,
      retryAttempts: 3
    },
    orders: {
      baseUrl: 'https://orders-api.company.com',
      version: 'v1', 
      timeout: 45000,
      retryAttempts: 1
    },
    analytics: {
      baseUrl: 'https://analytics-api.company.com',
      version: 'v3',
      timeout: 60000,
      retryAttempts: 2
    },
    payments: {
      baseUrl: 'https://payments-gateway.company.com',
      version: 'v1',
      timeout: 20000,
      retryAttempts: 3
    }
  };
}
```


### **1. Configuração nos Environments:**

## Exemplo Prático - Como a Injeção de Dependência Funciona:

### environments/environment.ts
```typescript
export const environment = {
  production: false,
  apis: {
    products: {
      baseUrl: 'http://localhost:3001/products-api.dev.company.com',
      version: 'v1',
      timeout: 30000,
      retryAttempts: 2
    },
    users: {
      baseUrl: 'http://localhost:3001/users-api.dev.company.com',
      version: 'v2',
      timeout: 15000,
      retryAttempts: 3
    },
    orders: {
      baseUrl: 'http://localhost:3001/orders-api.dev.company.com',
      version: 'v1',
      timeout: 45000,
      retryAttempts: 1
    },
    analytics: {
      baseUrl: 'http://localhost:3001/analytics-api.dev.company.com',
      version: 'v3',
      timeout: 60000,
      retryAttempts: 2
    },
    payments: {
      baseUrl: 'http://localhost:3001/payments-gateway.dev.company.com',
      version: 'v1',
      timeout: 20000,
      retryAttempts: 3
    }
  },
  features: {
    analytics: true,
    payments: true,
    advancedReporting: false
  }
};
```


**environment.prod.ts (produção):**

```typescript
export const environment = {
  production: true,
  apis: {
    products: {
      baseUrl: 'https://api-prod.empresa.com',  // API real
      version: 'v1', 
      timeout: 30000
    }
  }
};
```

### **2. Factory Function (CORRIGIDA):**

typescript

```typescript
import { environment } from '../../../environments/environment';

export function createApiEndpointsConfig(): ApiEndpointsConfig {
  // Retorna a configuração do environment atual
  return environment.apis;
}
```

### **3. Service Usando Injeção:**


```typescript
@Injectable()
export class ProductsApiService extends BaseApiService {
  constructor(
    http: HttpClient,
    @Inject(API_ENDPOINTS_CONFIG) private config: ApiEndpointsConfig
  ) {
    super(http);
    // Pega a configuração que veio do environment
    this.apiConfig = this.config.products;
  }

  getProducts() {
    // Vai usar a URL correta automaticamente:
    // DEV: http://localhost:3001/api/v1/products
    // PROD: https://api-prod.empresa.com/api/v1/products
    return this.get<ProductsResponse>('products');
  }
}
```

### **4. Como o Angular Resolve:**

**Durante desenvolvimento (ng serve):**

- Angular usa `environment.ts`
- Factory retorna `{ products: { baseUrl: 'http://localhost:3001' } }`
- Service recebe configuração local
- Chamadas vão para `localhost:3001`

**Durante build de produção (ng build --prod):**

- Angular usa `environment.prod.ts`
- Factory retorna `{ products: { baseUrl: 'https://api-prod.empresa.com' } }`
- Service recebe configuração de produção
- Chamadas vão para `api-prod.empresa.com`

### **5. Resultado Prático:**

O **mesmo código** do service funciona em qualquer ambiente:

typescript

```typescript
// Este código não muda entre dev/prod
this.productsApi.getProducts().subscribe(products => {
  console.log(products); // Dados vêm da URL correta automaticamente
});
```

**O Angular resolve qual URL usar baseado no build/ambiente**, sem o desenvolvedor precisar mudar nada no código do service.