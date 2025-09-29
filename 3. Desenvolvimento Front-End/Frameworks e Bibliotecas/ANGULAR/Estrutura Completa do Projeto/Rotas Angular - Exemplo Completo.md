## 1. Estrutura de Arquivos

```
src/app/
├── app.routes.ts                    # Rotas principais
├── app.component.ts                 # Componente raiz
├── core/
│   └── guards/
│       ├── auth.guard.ts
│       └── admin.guard.ts
├── layout/
│   ├── main-layout/
│   │   └── main-layout.component.ts
│   └── admin-layout/
│       └── admin-layout.component.ts
└── features/
    ├── auth/
    │   ├── auth.routes.ts
    │   ├── login/
    │   └── register/
    ├── products/
    │   ├── product.routes.ts
    │   ├── pages/
    │   │   ├── product-list/
    │   │   ├── product-detail/
    │   │   └── product-edit/
    │   └── resolvers/
    │       └── product.resolver.ts
    └── users/
        ├── user.routes.ts
        └── pages/
```

## 2. Rotas Principais (app.routes.ts)

typescript

```typescript
import { Routes } from '@angular/router';
import { authGuard } from './core/guards/auth.guard';
import { adminGuard } from './core/guards/admin.guard';
import { MainLayoutComponent } from './layout/main-layout/main-layout.component';
import { AdminLayoutComponent } from './layout/admin-layout/admin-layout.component';

export const routes: Routes = [
  // Redirect inicial
  {
    path: '',
    redirectTo: '/dashboard',
    pathMatch: 'full'
  },

  // Layout principal para usuários comuns
  {
    path: '',
    component: MainLayoutComponent,
    canActivate: [authGuard], // Proteção por autenticação
    children: [
      {
        path: 'dashboard',
        loadComponent: () => 
          import('./features/dashboard/dashboard.component')
            .then(c => c.DashboardComponent),
        data: { 
          title: 'Dashboard',
          breadcrumb: 'Início'
        }
      },
      {
        path: 'products',
        loadChildren: () => 
          import('./features/products/product.routes')
            .then(r => r.productRoutes),
        data: { 
          title: 'Produtos',
          breadcrumb: 'Produtos'
        }
      },
      {
        path: 'profile',
        loadComponent: () => 
          import('./features/profile/profile.component')
            .then(c => c.ProfileComponent),
        canActivate: [authGuard],
        data: { 
          title: 'Meu Perfil',
          breadcrumb: 'Perfil'
        }
      }
    ]
  },

  // Layout administrativo
  {
    path: 'admin',
    component: AdminLayoutComponent,
    canActivate: [authGuard, adminGuard], // Dupla proteção
    children: [
      {
        path: '',
        redirectTo: 'dashboard',
        pathMatch: 'full'
      },
      {
        path: 'dashboard',
        loadComponent: () => 
          import('./features/admin/admin-dashboard.component')
            .then(c => c.AdminDashboardComponent),
        data: { 
          title: 'Painel Administrativo',
          breadcrumb: 'Admin Dashboard'
        }
      },
      {
        path: 'users',
        loadChildren: () => 
          import('./features/users/user.routes')
            .then(r => r.userRoutes),
        data: { 
          title: 'Gerenciar Usuários',
          breadcrumb: 'Usuários'
        }
      },
      {
        path: 'settings',
        loadComponent: () => 
          import('./features/admin/settings.component')
            .then(c => c.SettingsComponent),
        data: { 
          title: 'Configurações do Sistema',
          breadcrumb: 'Configurações'
        }
      }
    ]
  },

  // Rotas de autenticação (sem layout)
  {
    path: 'auth',
    loadChildren: () => 
      import('./features/auth/auth.routes')
        .then(r => r.authRoutes)
  },

  // Página de erro 404
  {
    path: '404',
    loadComponent: () => 
      import('./shared/components/not-found/not-found.component')
        .then(c => c.NotFoundComponent),
    data: { title: 'Página não encontrada' }
  },

  // Wildcard - sempre por último
  {
    path: '**',
    redirectTo: '/404'
  }
];
```

## 3. Rotas de Feature - Products (product.routes.ts)

typescript

```typescript
import { Routes } from '@angular/router';
import { productResolver } from './resolvers/product.resolver';
import { canDeactivateGuard } from '../../core/guards/can-deactivate.guard';

export const productRoutes: Routes = [
  {
    path: '',
    loadComponent: () => 
      import('./pages/product-list/product-list.component')
        .then(c => c.ProductListComponent),
    data: { 
      title: 'Lista de Produtos',
      breadcrumb: 'Lista'
    }
  },
  {
    path: 'create',
    loadComponent: () => 
      import('./pages/product-create/product-create.component')
        .then(c => c.ProductCreateComponent),
    canDeactivate: [canDeactivateGuard], // Previne saída sem salvar
    data: { 
      title: 'Criar Produto',
      breadcrumb: 'Novo Produto'
    }
  },
  {
    path: ':id',
    loadComponent: () => 
      import('./pages/product-detail/product-detail.component')
        .then(c => c.ProductDetailComponent),
    resolve: {
      product: productResolver // Pre-carrega dados
    },
    data: { 
      title: 'Detalhes do Produto',
      breadcrumb: 'Detalhes'
    }
  },
  {
    path: ':id/edit',
    loadComponent: () => 
      import('./pages/product-edit/product-edit.component')
        .then(c => c.ProductEditComponent),
    resolve: {
      product: productResolver
    },
    canDeactivate: [canDeactivateGuard],
    data: { 
      title: 'Editar Produto',
      breadcrumb: 'Editar'
    }
  },
  {
    path: 'category/:categoryId',
    loadComponent: () => 
      import('./pages/product-list/product-list.component')
        .then(c => c.ProductListComponent),
    data: { 
      title: 'Produtos por Categoria',
      breadcrumb: 'Categoria'
    }
  }
];
```

## 4. Rotas de Autenticação (auth.routes.ts)

typescript

```typescript
import { Routes } from '@angular/router';
import { guestGuard } from '../../core/guards/guest.guard';

export const authRoutes: Routes = [
  {
    path: '',
    redirectTo: 'login',
    pathMatch: 'full'
  },
  {
    path: 'login',
    loadComponent: () => 
      import('./login/login.component')
        .then(c => c.LoginComponent),
    canActivate: [guestGuard], // Só acessa se não estiver logado
    data: { 
      title: 'Entrar',
      hideNavigation: true // Flag para esconder navegação
    }
  },
  {
    path: 'register',
    loadComponent: () => 
      import('./register/register.component')
        .then(c => c.RegisterComponent),
    canActivate: [guestGuard],
    data: { 
      title: 'Registrar-se',
      hideNavigation: true
    }
  },
  {
    path: 'forgot-password',
    loadComponent: () => 
      import('./forgot-password/forgot-password.component')
        .then(c => c.ForgotPasswordComponent),
    canActivate: [guestGuard],
    data: { 
      title: 'Esqueci minha senha',
      hideNavigation: true
    }
  },
  {
    path: 'reset-password/:token',
    loadComponent: () => 
      import('./reset-password/reset-password.component')
        .then(c => c.ResetPasswordComponent),
    data: { 
      title: 'Redefinir senha',
      hideNavigation: true
    }
  }
];
```

## 5. Guards (Proteção de Rotas)

### auth.guard.ts

typescript

```typescript
import { inject } from '@angular/core';
import { Router, CanActivateFn } from '@angular/router';
import { AuthService } from '../services/auth.service';

export const authGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (authService.isAuthenticated()) {
    return true;
  }

  // Salva URL de destino para redirect após login
  router.navigate(['/auth/login'], {
    queryParams: { returnUrl: state.url }
  });
  
  return false;
};
```

### admin.guard.ts

typescript

```typescript
import { inject } from '@angular/core';
import { Router, CanActivateFn } from '@angular/router';
import { AuthService } from '../services/auth.service';

export const adminGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  const user = authService.getCurrentUser();
  
  if (user && user.role === 'admin') {
    return true;
  }

  router.navigate(['/dashboard']);
  return false;
};
```

### can-deactivate.guard.ts

typescript

```typescript
import { CanDeactivateFn } from '@angular/router';

export interface CanComponentDeactivate {
  canDeactivate(): boolean | Promise<boolean>;
}

export const canDeactivateGuard: CanDeactivateFn<CanComponentDeactivate> = (component) => {
  return component.canDeactivate ? component.canDeactivate() : true;
};
```

## 6. Resolver (Pre-carregamento de Dados)

### product.resolver.ts

typescript

```typescript
import { inject } from '@angular/core';
import { Router, ResolveFn, ActivatedRouteSnapshot } from '@angular/router';
import { Observable, of } from 'rxjs';
import { catchError } from 'rxjs/operators';
import { ProductService } from '../services/product.service';
import { Product } from '../models/product.interface';

export const productResolver: ResolveFn<Product | null> = (
  route: ActivatedRouteSnapshot
): Observable<Product | null> => {
  const productService = inject(ProductService);
  const router = inject(Router);
  
  const id = Number(route.paramMap.get('id'));
  
  if (!id || isNaN(id)) {
    router.navigate(['/products']);
    return of(null);
  }

  return productService.getProductById(id).pipe(
    catchError(() => {
      router.navigate(['/products']);
      return of(null);
    })
  );
};
```

## 7. Navegação Programática nos Componentes

### Exemplo no Product List Component

typescript

```typescript
import { Component, inject } from '@angular/core';
import { Router, ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-product-list',
  template: `
    <div class="product-list">
      <button (click)="createProduct()">Novo Produto</button>
      
      <div *ngFor="let product of products" class="product-card">
        <h3>{{ product.name }}</h3>
        <button (click)="viewProduct(product.id)">Ver</button>
        <button (click)="editProduct(product.id)">Editar</button>
        <button (click)="deleteProduct(product.id)">Excluir</button>
      </div>
    </div>
  `
})
export class ProductListComponent {
  private router = inject(Router);
  private route = inject(ActivatedRoute);
  
  products: Product[] = [];

  // Navegação relativa
  createProduct() {
    this.router.navigate(['./create'], { 
      relativeTo: this.route 
    });
  }

  // Navegação com parâmetros
  viewProduct(id: number) {
    this.router.navigate(['./', id], { 
      relativeTo: this.route 
    });
  }

  // Navegação absoluta
  editProduct(id: number) {
    this.router.navigate(['/products', id, 'edit']);
  }

  // Navegação com query params
  filterByCategory(categoryId: number) {
    this.router.navigate(['/products'], {
      queryParams: { category: categoryId, page: 1 }
    });
  }

  // Navegação com confirmação
  deleteProduct(id: number) {
    if (confirm('Deseja excluir este produto?')) {
      // Lógica de exclusão
      this.router.navigate(['/products']);
    }
  }
}
```

## 8. Template com Router Links

html

```html
<!-- Navegação no template -->
<nav class="main-nav">
  <!-- Link simples -->
  <a routerLink="/dashboard" routerLinkActive="active">Dashboard</a>
  
  <!-- Link com parâmetros -->
  <a [routerLink]="['/products', product.id]" routerLinkActive="active">
    {{ product.name }}
  </a>
  
  <!-- Link relativo -->
  <a routerLink="../list" routerLinkActive="active">Voltar</a>
  
  <!-- Link com query params -->
  <a [routerLink]="['/products']" 
     [queryParams]="{ category: 'electronics', page: 1 }"
     routerLinkActive="active">
    Eletrônicos
  </a>
  
  <!-- Link com fragment -->
  <a routerLink="/help" fragment="faq" routerLinkActive="active">FAQ</a>
</nav>

<!-- Outlet para rotas filhas -->
<main class="content">
  <router-outlet></router-outlet>
</main>
```

## 9. Leitura de Parâmetros e Query Params

typescript

```typescript
import { Component, OnInit, inject } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';

@Component({
  selector: 'app-product-detail',
  template: `
    <div *ngIf="product">
      <h1>{{ product.name }}</h1>
      <p>ID: {{ productId }}</p>
      <p>Categoria: {{ categoryFilter }}</p>
      <p>Página: {{ currentPage }}</p>
    </div>
  `
})
export class ProductDetailComponent implements OnInit {
  private route = inject(ActivatedRoute);
  private router = inject(Router);
  
  product: Product;
  productId: number;
  categoryFilter: string;
  currentPage: number;

  ngOnInit() {
    // Parâmetros da rota (ex: /products/:id)
    this.route.paramMap.subscribe(params => {
      this.productId = Number(params.get('id'));
      this.loadProduct();
    });

    // Query parameters (ex: ?category=electronics&page=1)
    this.route.queryParamMap.subscribe(queryParams => {
      this.categoryFilter = queryParams.get('category') || '';
      this.currentPage = Number(queryParams.get('page')) || 1;
    });

    // Dados do resolver
    this.route.data.subscribe(data => {
      this.product = data['product'];
    });

    // Fragment (ex: #section1)
    this.route.fragment.subscribe(fragment => {
      if (fragment) {
        document.getElementById(fragment)?.scrollIntoView();
      }
    });
  }

  private loadProduct() {
    // Carregar produto baseado no ID
  }
}
```

## 10. URLs Resultantes

Com esta configuração, as URLs ficam:

```
/                           → Redirect para /dashboard
/dashboard                  → Dashboard principal
/products                   → Lista de produtos
/products/create           → Criar produto
/products/123              → Detalhes do produto 123
/products/123/edit         → Editar produto 123
/products/category/5       → Produtos da categoria 5
/admin                     → Redirect para /admin/dashboard
/admin/dashboard           → Painel administrativo
/admin/users               → Gerenciar usuários
/admin/settings            → Configurações
/auth/login                → Página de login
/auth/register             → Página de registro
/auth/forgot-password      → Esqueci senha
/auth/reset-password/token → Redefinir senha
/404                       → Página não encontrada
/qualquer-coisa           → Redirect para /404
```

Esta estrutura oferece navegação completa com proteção, lazy loading, pre-carregamento de dados e layouts diferenciados.