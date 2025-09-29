
## 1. Conceito e Funcionamento

### **O que é Auth Guard:**

Mecanismo de proteção que intercepta navegação e verifica se usuário tem permissão para acessar uma rota.

### **Quando é executado:**

- Antes de carregar o componente
- Antes de ativar a rota
- Pode redirecionar para login ou página de erro

---

## 2. Implementação Básica

### **Auth Guard (Functional Guard - Angular 14+):**

typescript

```typescript
// core/guards/auth.guard.ts
import { inject } from '@angular/core';
import { Router, CanActivateFn } from '@angular/router';
import { AuthService } from '../services/auth.service';

export const authGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  // Verificar se está autenticado
  if (authService.isAuthenticated()) {
    return true; // Permite acesso
  }

  // Salvar URL de destino para redirect após login
  router.navigate(['/auth/login'], {
    queryParams: { returnUrl: state.url }
  });
  
  return false; // Bloqueia acesso
};
```

### **Auth Service de Apoio:**

typescript

```typescript
// core/services/auth.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

interface User {
  id: number;
  name: string;
  email: string;
  role: string;
  permissions: string[];
}

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private currentUserSubject = new BehaviorSubject<User | null>(null);
  public currentUser$ = this.currentUserSubject.asObservable();

  constructor() {
    this.loadUserFromStorage();
  }

  isAuthenticated(): boolean {
    const token = this.getToken();
    
    if (!token) {
      return false;
    }

    // Verificar se token não expirou
    return !this.isTokenExpired(token);
  }

  getToken(): string | null {
    return localStorage.getItem('authToken');
  }

  getCurrentUser(): User | null {
    return this.currentUserSubject.value;
  }

  login(email: string, password: string): Observable<any> {
    // Lógica de login
    return this.http.post('/api/auth/login', { email, password });
  }

  logout(): void {
    localStorage.removeItem('authToken');
    localStorage.removeItem('currentUser');
    this.currentUserSubject.next(null);
  }

  private isTokenExpired(token: string): boolean {
    try {
      const payload = JSON.parse(atob(token.split('.')[1]));
      const currentTime = Math.floor(Date.now() / 1000);
      return payload.exp < currentTime;
    } catch {
      return true;
    }
  }

  private loadUserFromStorage(): void {
    const userJson = localStorage.getItem('currentUser');
    if (userJson && this.isAuthenticated()) {
      const user = JSON.parse(userJson);
      this.currentUserSubject.next(user);
    }
  }
}
```

---

## 3. Configuração nas Rotas

### **Proteção Básica:**

typescript

```typescript
// app.routes.ts
import { Routes } from '@angular/router';
import { authGuard } from './core/guards/auth.guard';

export const routes: Routes = [
  // Rotas públicas (sem guard)
  {
    path: 'auth',
    loadChildren: () => import('./features/auth/auth.routes').then(r => r.authRoutes)
  },
  {
    path: 'home',
    loadComponent: () => import('./features/home/home.component').then(c => c.HomeComponent)
  },

  // Rotas protegidas (com guard)
  {
    path: 'dashboard',
    loadComponent: () => import('./features/dashboard/dashboard.component').then(c => c.DashboardComponent),
    canActivate: [authGuard] // Proteção aplicada
  },
  {
    path: 'profile',
    loadComponent: () => import('./features/profile/profile.component').then(c => c.ProfileComponent),
    canActivate: [authGuard]
  },

  // Proteção de módulos inteiros
  {
    path: 'admin',
    loadChildren: () => import('./features/admin/admin.routes').then(r => r.adminRoutes),
    canActivate: [authGuard] // Protege todas as rotas filhas
  }
];
```

### **Proteção de Layout:**

typescript

```typescript
// Layout principal protegido
{
  path: '',
  component: MainLayoutComponent,
  canActivate: [authGuard], // Protege todo o layout
  children: [
    {
      path: 'dashboard',
      loadComponent: () => import('./dashboard.component').then(c => c.DashboardComponent)
    },
    {
      path: 'products',
      loadChildren: () => import('./products/product.routes').then(r => r.productRoutes)
    },
    {
      path: 'orders',
      loadChildren: () => import('./orders/order.routes').then(r => r.orderRoutes)
    }
  ]
}
```

---

## 4. Guards Avançados

### **Role-based Guard:**

typescript

```typescript
// core/guards/role.guard.ts
import { inject } from '@angular/core';
import { Router, CanActivateFn } from '@angular/router';
import { AuthService } from '../services/auth.service';

export const roleGuard = (allowedRoles: string[]): CanActivateFn => {
  return (route, state) => {
    const authService = inject(AuthService);
    const router = inject(Router);

    if (!authService.isAuthenticated()) {
      router.navigate(['/auth/login']);
      return false;
    }

    const user = authService.getCurrentUser();
    
    if (user && allowedRoles.includes(user.role)) {
      return true;
    }

    // Usuário autenticado mas sem permissão
    router.navigate(['/unauthorized']);
    return false;
  };
};
```

### **Permission-based Guard:**

typescript

```typescript
// core/guards/permission.guard.ts
export const permissionGuard = (requiredPermissions: string[]): CanActivateFn => {
  return (route, state) => {
    const authService = inject(AuthService);
    const router = inject(Router);

    if (!authService.isAuthenticated()) {
      router.navigate(['/auth/login']);
      return false;
    }

    const user = authService.getCurrentUser();
    
    if (!user) {
      router.navigate(['/auth/login']);
      return false;
    }

    // Verificar se tem todas as permissões necessárias
    const hasAllPermissions = requiredPermissions.every(permission => 
      user.permissions.includes(permission)
    );

    if (hasAllPermissions) {
      return true;
    }

    router.navigate(['/unauthorized']);
    return false;
  };
};
```

### **Admin Guard:**

typescript

```typescript
// core/guards/admin.guard.ts
export const adminGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (!authService.isAuthenticated()) {
    router.navigate(['/auth/login']);
    return false;
  }

  const user = authService.getCurrentUser();
  
  if (user && user.role === 'admin') {
    return true;
  }

  // Redirecionar para área do usuário comum
  router.navigate(['/dashboard']);
  return false;
};
```

---

## 5. Uso dos Guards nas Rotas

### **Configuração com Múltiplos Guards:**

typescript

```typescript
export const routes: Routes = [
  // Apenas autenticado
  {
    path: 'dashboard',
    loadComponent: () => import('./dashboard.component').then(c => c.DashboardComponent),
    canActivate: [authGuard]
  },

  // Role específico
  {
    path: 'admin',
    loadChildren: () => import('./admin/admin.routes').then(r => r.adminRoutes),
    canActivate: [authGuard, roleGuard(['admin', 'super-admin'])]
  },

  // Permissões específicas
  {
    path: 'reports',
    loadComponent: () => import('./reports.component').then(c => c.ReportsComponent),
    canActivate: [authGuard, permissionGuard(['reports.view', 'analytics.access'])]
  },

  // Múltiplas verificações
  {
    path: 'settings',
    loadComponent: () => import('./settings.component').then(c => c.SettingsComponent),
    canActivate: [
      authGuard, 
      roleGuard(['admin']), 
      permissionGuard(['settings.manage'])
    ]
  }
];
```

### **Guards em Rotas Filhas:**

typescript

```typescript
// admin.routes.ts
export const adminRoutes: Routes = [
  {
    path: '',
    component: AdminLayoutComponent,
    canActivate: [authGuard, adminGuard], // Proteção do layout
    children: [
      {
        path: 'users',
        loadComponent: () => import('./users.component').then(c => c.UsersComponent),
        canActivate: [permissionGuard(['users.manage'])] // Proteção adicional
      },
      {
        path: 'system',
        loadComponent: () => import('./system.component').then(c => c.SystemComponent),
        canActivate: [permissionGuard(['system.admin'])]
      }
    ]
  }
];
```

---

## 6. Guards com Dados da Rota

### **Guard que usa parâmetros da rota:**

typescript

```typescript
// core/guards/resource-owner.guard.ts
export const resourceOwnerGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (!authService.isAuthenticated()) {
    router.navigate(['/auth/login']);
    return false;
  }

  const user = authService.getCurrentUser();
  const resourceUserId = Number(route.paramMap.get('userId'));

  // Usuário só pode acessar seus próprios recursos
  if (user && (user.id === resourceUserId || user.role === 'admin')) {
    return true;
  }

  router.navigate(['/unauthorized']);
  return false;
};
```

### **Uso do Guard com parâmetros:**

typescript

```typescript
{
  path: 'user/:userId/profile',
  loadComponent: () => import('./user-profile.component').then(c => c.UserProfileComponent),
  canActivate: [authGuard, resourceOwnerGuard] // Verifica se é dono do recurso
}
```

---

## 7. Interceptação e Redirecionamento

### **Login Component com Return URL:**

typescript

```typescript
// features/auth/login/login.component.ts
import { Component, OnInit, inject } from '@angular/core';
import { Router, ActivatedRoute } from '@angular/router';
import { AuthService } from '../../../core/services/auth.service';

@Component({
  selector: 'app-login',
  template: `
    <form (ngSubmit)="onLogin()">
      <input [(ngModel)]="email" type="email" placeholder="Email" required>
      <input [(ngModel)]="password" type="password" placeholder="Senha" required>
      <button type="submit" [disabled]="loading">
        {{ loading ? 'Entrando...' : 'Entrar' }}
      </button>
    </form>
  `
})
export class LoginComponent implements OnInit {
  private authService = inject(AuthService);
  private router = inject(Router);
  private route = inject(ActivatedRoute);

  email = '';
  password = '';
  loading = false;
  returnUrl = '/dashboard'; // URL padrão

  ngOnInit() {
    // Capturar URL de retorno do query param
    this.returnUrl = this.route.snapshot.queryParams['returnUrl'] || '/dashboard';
  }

  onLogin() {
    this.loading = true;

    this.authService.login(this.email, this.password).subscribe({
      next: (response) => {
        // Salvar token e usuário
        localStorage.setItem('authToken', response.token);
        localStorage.setItem('currentUser', JSON.stringify(response.user));

        // Redirecionar para URL original
        this.router.navigateByUrl(this.returnUrl);
      },
      error: (error) => {
        console.error('Erro no login:', error);
        this.loading = false;
      }
    });
  }
}
```

---

## 8. Guard para Usuários Não Autenticados

### **Guest Guard (para páginas de login):**

typescript

```typescript
// core/guards/guest.guard.ts
export const guestGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  // Se já está logado, redireciona para dashboard
  if (authService.isAuthenticated()) {
    router.navigate(['/dashboard']);
    return false;
  }

  return true; // Permite acesso às páginas de auth
};
```

### **Uso do Guest Guard:**

typescript

```typescript
// auth.routes.ts
export const authRoutes: Routes = [
  {
    path: 'login',
    loadComponent: () => import('./login/login.component').then(c => c.LoginComponent),
    canActivate: [guestGuard] // Impede acesso se já logado
  },
  {
    path: 'register',
    loadComponent: () => import('./register/register.component').then(c => c.RegisterComponent),
    canActivate: [guestGuard]
  }
];
```

---

## 9. Guards Assíncronos

### **Guard com verificação assíncrona:**

typescript

```typescript
// core/guards/async-auth.guard.ts
export const asyncAuthGuard: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  return authService.validateToken().pipe(
    map(isValid => {
      if (isValid) {
        return true;
      }
      
      router.navigate(['/auth/login']);
      return false;
    }),
    catchError(() => {
      router.navigate(['/auth/login']);
      return of(false);
    })
  );
};
```

### **Auth Service com validação assíncrona:**

typescript

```typescript
validateToken(): Observable<boolean> {
  const token = this.getToken();
  
  if (!token) {
    return of(false);
  }

  // Validar token no servidor
  return this.http.post<{valid: boolean}>('/api/auth/validate', { token }).pipe(
    map(response => response.valid),
    catchError(() => of(false))
  );
}
```

---

## 10. Exemplo Completo de Uso

### **Aplicação com múltiplos níveis de proteção:**

typescript

```typescript
// app.routes.ts - Configuração completa
export const routes: Routes = [
  // Páginas públicas
  {
    path: '',
    loadComponent: () => import('./features/home/home.component').then(c => c.HomeComponent)
  },

  // Autenticação (só para não logados)
  {
    path: 'auth',
    canActivate: [guestGuard],
    children: [
      {
        path: 'login',
        loadComponent: () => import('./features/auth/login.component').then(c => c.LoginComponent)
      }
    ]
  },

  // Área do usuário (protegida)
  {
    path: '',
    component: MainLayoutComponent,
    canActivate: [authGuard],
    children: [
      {
        path: 'dashboard',
        loadComponent: () => import('./features/dashboard/dashboard.component').then(c => c.DashboardComponent)
      },
      {
        path: 'profile',
        loadComponent: () => import('./features/profile/profile.component').then(c => c.ProfileComponent)
      }
    ]
  },

  // Área administrativa (admin only)
  {
    path: 'admin',
    component: AdminLayoutComponent,
    canActivate: [authGuard, adminGuard],
    children: [
      {
        path: 'users',
        loadComponent: () => import('./features/admin/users.component').then(c => c.UsersComponent),
        canActivate: [permissionGuard(['users.manage'])]
      },
      {
        path: 'reports',
        loadComponent: () => import('./features/admin/reports.component').then(c => c.ReportsComponent),
        canActivate: [permissionGuard(['reports.view'])]
      }
    ]
  }
];
```

O Auth Guard é fundamental para proteger rotas e garantir que apenas usuários autorizados acessem determinadas áreas da aplicação. A implementação deve ser robusta e considerar diferentes níveis de permissão conforme a complexidade da aplicação.