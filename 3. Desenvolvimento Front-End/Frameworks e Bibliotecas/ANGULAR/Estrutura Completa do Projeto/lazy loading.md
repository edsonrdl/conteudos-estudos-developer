# Lazy Loading Angular - Guia Completo

## 1. Conceitos Fundamentais

### **O que é Lazy Loading?**

Carregamento preguiçoso que divide a aplicação em chunks menores, carregados apenas quando necessário.

### **Como Funciona:**

- **Build time:** Angular cria bundles separados
- **Runtime:** Carrega bundle apenas quando rota é acessada
- **Cache:** Browser cacheia bundles carregados

---

## 2. Lazy Loading com Componentes Standalone

### **Configuração Básica:**

typescript

```typescript
// app.routes.ts
export const routes: Routes = [
  {
    path: 'products',
    loadComponent: () => 
      import('./features/products/product-list.component')
        .then(c => c.ProductListComponent)
  },
  {
    path: 'users',
    loadComponent: () => 
      import('./features/users/user-list.component')
        .then(c => c.UserListComponent)
  }
];
```

### **Componente Standalone Completo:**

typescript

```typescript
// features/products/product-list.component.ts
import { Component, OnInit, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterLink } from '@angular/router';
import { ProductService } from './services/product.service';

@Component({
  selector: 'app-product-list',
  standalone: true,
  imports: [
    CommonModule,
    RouterLink,
    // Outros componentes necessários
  ],
  template: `
    <div class="product-list">
      <h1>Produtos</h1>
      <div *ngFor="let product of products" class="product-item">
        <h3>{{ product.name }}</h3>
        <p>{{ product.price | currency }}</p>
        <a [routerLink]="['/products', product.id]">Ver detalhes</a>
      </div>
    </div>
  `,
  providers: [
    // Services específicos do componente
    ProductService
  ]
})
export class ProductListComponent implements OnInit {
  private productService = inject(ProductService);
  products: any[] = [];

  ngOnInit() {
    this.loadProducts();
  }

  private loadProducts() {
    this.productService.getProducts().subscribe(products => {
      this.products = products;
    });
  }
}
```

### **Vantagens dos Componentes Standalone:**

- **Bundle minimalista:** Só carrega dependências específicas
- **Inicialização rápida:** Menos overhead de módulos
- **Flexibilidade:** Cada componente gerencia suas dependências
- **Tree-shaking otimizado:** Remoção automática de código não usado

### **Desvantagens dos Componentes Standalone:**

- **Imports repetitivos:** Cada componente importa suas dependências
- **Sem compartilhamento automático:** Precisa importar explicitamente
- **Configuração verbosa:** Mais código para setup inicial

---

## 3. Lazy Loading com Módulos

### **Configuração Modular:**

typescript

```typescript
// app.routes.ts
export const routes: Routes = [
  {
    path: 'products',
    loadChildren: () => 
      import('./features/products/products.module')
        .then(m => m.ProductsModule)
  },
  {
    path: 'admin',
    loadChildren: () => 
      import('./features/admin/admin.module')
        .then(m => m.AdminModule)
  }
];
```

### **Módulo Feature Completo:**

typescript

```typescript
// features/products/products.module.ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule } from '@angular/forms';

// Routing
import { ProductsRoutingModule } from './products-routing.module';

// Components
import { ProductListComponent } from './components/product-list/product-list.component';
import { ProductDetailComponent } from './components/product-detail/product-detail.component';
import { ProductFormComponent } from './components/product-form/product-form.component';

// Services
import { ProductService } from './services/product.service';
import { ProductCacheService } from './services/product-cache.service';

// Shared
import { SharedModule } from '../../shared/shared.module';

@NgModule({
  declarations: [
    ProductListComponent,
    ProductDetailComponent,
    ProductFormComponent
  ],
  imports: [
    CommonModule,
    ReactiveFormsModule,
    ProductsRoutingModule,
    SharedModule
  ],
  providers: [
    ProductService,
    ProductCacheService
  ]
})
export class ProductsModule { }
```

### **Routing do Módulo:**

typescript

```typescript
// features/products/products-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { ProductListComponent } from './components/product-list/product-list.component';
import { ProductDetailComponent } from './components/product-detail/product-detail.component';

const routes: Routes = [
  {
    path: '',
    component: ProductListComponent
  },
  {
    path: ':id',
    component: ProductDetailComponent
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class ProductsRoutingModule { }
```

### **Vantagens dos Módulos:**

- **Compartilhamento automático:** Services e components disponíveis para todo módulo
- **Configuração centralizada:** Imports, providers em um lugar
- **Lazy loading de funcionalidades completas:** Carrega feature inteira
- **Organização clara:** Estrutura bem definida

### **Desvantagens dos Módulos:**

- **Bundle maior:** Carrega todo o módulo mesmo usando só parte
- **Overhead:** Inicialização de módulo tem custo
- **Menos flexibilidade:** Todos components compartilham dependências
- **Complexidade:** Mais camadas de abstração

---

## 4. Casos de Uso Detalhados

### **Caso 1: E-commerce - Páginas de Produto**

#### **Cenário:** Loja online com catálogo extenso

**Standalone (Recomendado):**

typescript

```typescript
// Páginas independentes com carregamento otimizado
{
  path: 'products',
  loadComponent: () => import('./product-catalog.component').then(c => c.ProductCatalogComponent)
},
{
  path: 'product/:id',
  loadComponent: () => import('./product-detail.component').then(c => c.ProductDetailComponent)
},
{
  path: 'cart',
  loadComponent: () => import('./shopping-cart.component').then(c => c.ShoppingCartComponent)
}
```

**Vantagens:**

- **Bundle micro:** Usuário só baixa página que vai usar
- **Performance mobile:** Crítico para conversão
- **SEO otimizado:** Carregamento rápido da primeira página

**Quando usar:** Alta variabilidade de uso entre páginas

---

### **Caso 2: Sistema Administrativo - Módulo Completo**

#### **Cenário:** ERP com funcionalidades integradas

**Módulos (Recomendado):**

typescript

```typescript
// Funcionalidades que trabalham juntas
{
  path: 'admin',
  loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule)
},
{
  path: 'inventory',
  loadChildren: () => import('./inventory/inventory.module').then(m => m.InventoryModule)
}
```

**Vantagens:**

- **Funcionalidades coesas:** Usuário usa várias páginas da mesma feature
- **Services compartilhados:** Cache, validações, business logic
- **Carregamento em bloco:** Uma vez carregado, navegação instantânea

**Quando usar:** Features com alta coesão interna

---

### **Caso 3: Blog/CMS - Páginas Específicas**

#### **Cenário:** Site de conteúdo com seções distintas

**Standalone (Recomendado):**

typescript

```typescript
{
  path: 'blog',
  loadComponent: () => import('./blog/blog-list.component').then(c => c.BlogListComponent)
},
{
  path: 'about',
  loadComponent: () => import('./pages/about.component').then(c => c.AboutComponent)
},
{
  path: 'contact',
  loadComponent: () => import('./pages/contact.component').then(c => c.ContactComponent)
}
```

**Vantagens:**

- **SEO máximo:** Cada página carrega apenas o necessário
- **Flexibilidade de layout:** Páginas podem ter estruturas diferentes
- **Manutenção simples:** Páginas independentes

---

### **Caso 4: Dashboard Analítico - Módulo Integrado**

#### **Cenário:** Business Intelligence com múltiplos gráficos

**Módulos (Recomendado):**

typescript

```typescript
{
  path: 'analytics',
  loadChildren: () => import('./analytics/analytics.module').then(m => m.AnalyticsModule)
}

// analytics.module.ts
@NgModule({
  imports: [
    ChartsModule,      // Biblioteca de gráficos pesada
    DatePickerModule,  // Componentes de data
    ExportModule       // Funcionalidades de export
  ],
  // Todos components compartilham essas dependências pesadas
})
```

**Vantagens:**

- **Libraries pesadas compartilhadas:** Chart.js carregado uma vez
- **State management:** Dados compartilhados entre dashboards
- **Configuração única:** Temas, formatações centralizadas

---

## 5. Análise de Performance

### **Métricas de Bundle Size:**

|Tipo|Bundle Inicial|Bundle Feature|Total|
|---|---|---|---|
|**Standalone**|50kb|15kb por página|50kb + (15kb × páginas visitadas)|
|**Módulo**|50kb|80kb por módulo|50kb + (80kb × módulos visitados)|

### **Tempo de Carregamento:**

|Cenário|Primeira Página|Segunda Página|Terceira Página|
|---|---|---|---|
|**Standalone**|1.2s|0.8s|0.8s|
|**Módulo**|2.1s|0.1s|0.1s|

---

## 6. Estratégias Avançadas

### **Preloading Strategy Customizada:**

typescript

```typescript
// core/strategies/selective-preload.strategy.ts
import { Injectable } from '@angular/core';
import { PreloadingStrategy, Route } from '@angular/router';
import { Observable, of, timer } from 'rxjs';
import { switchMap } from 'rxjs/operators';

@Injectable()
export class SelectivePreloadStrategy implements PreloadingStrategy {
  
  preload(route: Route, fn: () => Observable<any>): Observable<any> {
    // Preload baseado em prioridade
    if (route.data?.['preload'] === 'high') {
      return fn(); // Carrega imediatamente
    }
    
    if (route.data?.['preload'] === 'medium') {
      return timer(2000).pipe(switchMap(() => fn())); // 2s delay
    }
    
    if (route.data?.['preload'] === 'low') {
      return timer(10000).pipe(switchMap(() => fn())); // 10s delay
    }
    
    return of(null); // Não preload
  }
}

// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes, withPreloading(SelectivePreloadStrategy))
  ]
};
```

### **Configuração de Rotas com Prioridades:**

typescript

```typescript
export const routes: Routes = [
  {
    path: 'dashboard',
    loadComponent: () => import('./dashboard.component').then(c => c.DashboardComponent),
    data: { preload: 'high' } // Usuário provavelmente vai acessar
  },
  {
    path: 'products',
    loadChildren: () => import('./products/products.module').then(m => m.ProductsModule),
    data: { preload: 'medium' } // Acesso moderado
  },
  {
    path: 'admin',
    loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule),
    data: { preload: 'low' } // Poucos usuários acessam
  }
];
```

---

## 7. Boas Práticas e Otimizações

### **Bundle Analysis:**

bash

```bash
# Analisar tamanho dos bundles
ng build --stats-json
npx webpack-bundle-analyzer dist/your-app/stats.json
```

### **Otimizações Específicas:**

#### **Para Standalone Components:**

typescript

```typescript
// Lazy load até imports pesados
@Component({
  standalone: true,
  imports: [
    CommonModule,
    // Não importar bibliotecas pesadas aqui
  ]
})
export class MyComponent {
  async loadHeavyLibrary() {
    // Lazy load biblioteca pesada apenas quando necessário
    const { HeavyChartComponent } = await import('./heavy-chart.component');
    // Usar dinamicamente
  }
}
```

#### **Para Módulos:**

typescript

```typescript
// Compartilhar dependências pesadas
@NgModule({
  imports: [
    ChartsModule.forRoot(), // Configuração uma vez
    SharedModule            // Components reutilizáveis
  ]
})
export class AnalyticsModule {}
```

---

## 8. Decisão: Standalone vs Módulos

### **Use Standalone quando:**

- Páginas independentes (landing pages, blog posts)
- E-commerce com alta variabilidade de uso
- Sites de conteúdo com SEO crítico
- Aplicações PWA com foco em performance
- Prototipagem rápida

### **Use Módulos quando:**

- Sistemas administrativos complexos
- Dashboards com múltiplas visualizações
- Features com alta coesão (CRM, ERP)
- Bibliotecas pesadas compartilhadas (Charts, Maps)
- Equipes grandes com features isoladas

### **Híbrido (Recomendado para apps grandes):**

typescript

```typescript
export const routes: Routes = [
  // Páginas simples - Standalone
  { path: 'home', loadComponent: () => import('./home.component').then(c => c.HomeComponent) },
  { path: 'about', loadComponent: () => import('./about.component').then(c => c.AboutComponent) },
  
  // Features complexas - Módulos
  { path: 'admin', loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule) },
  { path: 'analytics', loadChildren: () => import('./analytics/analytics.module').then(m => m.AnalyticsModule) }
];
```

O lazy loading é essencial para aplicações modernas, mas a escolha entre standalone e módulos deve ser baseada no padrão de uso real dos usuários e na arquitetura da aplicação.