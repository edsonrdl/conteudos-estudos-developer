## Contexto e Usabilidade da Camada **Layout**

### **O que é a Camada Layout?**

A camada Layout define **a estrutura visual e organizacional** da aplicação - é literalmente "como as coisas ficam dispostas na tela" para diferentes tipos de usuários e contextos.

### **Por que existe?**

**Problema:** Sem layouts, cada página teria que recriar header, sidebar, footer, etc. Isso gera inconsistência visual e duplicação de código.

**Solução:** Centralizar a estrutura visual comum em componentes reutilizáveis.

### **Tipos Comuns de Layout:**

#### **1. Main Layout (Usuário Comum)**

- **Contexto:** Usuários finais navegando no sistema
- **Características:** Header simples, sidebar com funcionalidades básicas, foco na usabilidade
- **Usado em:** Dashboard, produtos, pedidos, perfil

#### **2. Admin Layout (Administrador)**

- **Contexto:** Gestores e administradores
- **Características:** Header com mais opções, sidebar com controles avançados, painéis de estatísticas
- **Usado em:** Gerenciamento de usuários, analytics, relatórios, configurações do sistema

#### **3. Auth Layout (Autenticação)**

- **Contexto:** Usuários não logados
- **Características:** Layout minimalista, sem sidebar/header, foco no formulário
- **Usado em:** Login, registro, recuperação de senha

#### **4. Public Layout (Site Público)**

- **Contexto:** Visitantes do site
- **Características:** Header marketing, footer com links, design mais "comercial"
- **Usado em:** Landing page, sobre, contato

### **Usabilidade na Prática:**

#### **Cenário 1: E-commerce**

- **Main Layout:** Clientes navegam produtos, fazem pedidos
- **Admin Layout:** Gestores controlam estoque, veem relatórios
- **Public Layout:** Visitantes veem produtos sem fazer login

#### **Cenário 2: Sistema Empresarial**

- **Main Layout:** Funcionários acessam suas tarefas diárias
- **Admin Layout:** RH gerencia usuários, TI vê logs do sistema
- **Auth Layout:** Processo de login corporativo

#### **Cenário 3: SaaS (Software as a Service)**

- **Main Layout:** Usuários finais usam as funcionalidades principais
- **Admin Layout:** Administradores da empresa cliente
- **Super Admin Layout:** Equipe do SaaS gerencia múltiplos clientes

### **Benefícios Práticos:**

#### **Para o Usuário:**

- **Consistência:** Navegação sempre no mesmo lugar
- **Familiaridade:** Aprender uma vez, usar em toda aplicação
- **Eficiência:** Acesso rápido às funcionalidades principais

#### **Para o Desenvolvedor:**

- **Reutilização:** Escrever header/sidebar uma vez só
- **Manutenção:** Mudança no layout afeta toda aplicação
- **Organização:** Separação clara entre estrutura e conteúdo

#### **Para o Negócio:**

- **Branding:** Identidade visual consistente
- **Experiência:** UX padronizada reduz curva de aprendizado
- **Produtividade:** Usuários navegam mais eficientemente

### **Quando Usar Cada Layout:**

#### **Main Layout - Quando:**

- Usuário está logado
- Acessa funcionalidades principais
- Precisa navegar entre diferentes seções
- Foco na produtividade diária

#### **Admin Layout - Quando:**

- Usuário tem permissões administrativas
- Precisa de visão geral do sistema
- Acessa relatórios e configurações
- Gerencia outros usuários

#### **Auth Layout - Quando:**

- Usuário não está autenticado
- Processo de login/registro
- Funcionalidades públicas específicas
- Foco na conversão (fazer login)

### **Impacto na Arquitetura:**

#### **Roteamento:**

Layouts determinam **qual estrutura visual** cada rota vai usar, permitindo experiências diferenciadas para diferentes tipos de usuário.

#### **Permissões:**

Layouts podem mostrar/esconder funcionalidades baseado no **nível de acesso** do usuário.

#### **Responsividade:**

Cada layout pode ter **comportamentos mobile** específicos - admin layout pode ser mais complexo no desktop, mais simples no mobile.

### **Analogia do Mundo Real:**

Pense numa **loja física**:

- **Área do cliente:** Layout atrativo, fácil navegação, foco na compra
- **Área administrativa:** Layout funcional, acesso a sistemas, relatórios
- **Entrada da loja:** Layout acolhedor, informações básicas

O mesmo produto (software) precisa de **"decorações" diferentes** dependendo de **quem está usando** e **para que**.

A camada Layout é essencialmente sobre **adaptar a mesma aplicação** para **diferentes contextos de uso**, mantendo a funcionalidade mas mudando a apresentação e organização conforme a necessidade do usuário.

### **Layout** (Estrutura Visual)

- **Templates de layout** da aplicação
- Define a **estrutura visual comum**
- Reutilizado por múltiplas features

---

## Exemplo da Camada Layout:

### **layout/main-layout/main-layout.component.ts**

typescript

```typescript
import { Component, OnInit, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterOutlet, Router, NavigationEnd } from '@angular/router';
import { filter } from 'rxjs/operators';

// Layout Components
import { HeaderComponent } from '../header/header.component';
import { SidebarComponent } from '../sidebar/sidebar.component';
import { FooterComponent } from '../footer/footer.component';
import { BreadcrumbComponent } from '../breadcrumb/breadcrumb.component';

// Services
import { AuthService } from '../../core/services/auth.service';
import { LayoutService } from '../services/layout.service';

@Component({
  selector: 'app-main-layout',
  standalone: true,
  imports: [
    CommonModule,
    RouterOutlet,
    HeaderComponent,
    SidebarComponent,
    FooterComponent,
    BreadcrumbComponent
  ],
  template: `
    <div class="layout-container" [class.sidebar-collapsed]="layoutService.sidebarCollapsed">
      <!-- Header Global -->
      <app-header 
        (toggleSidebar)="layoutService.toggleSidebar()"
        (logout)="onLogout()">
      </app-header>

      <div class="layout-content">
        <!-- Sidebar de Navegação -->
        <app-sidebar 
          [collapsed]="layoutService.sidebarCollapsed"
          [currentUser]="currentUser">
        </app-sidebar>

        <!-- Área Principal de Conteúdo -->
        <main class="main-content">
          <!-- Breadcrumb -->
          <app-breadcrumb></app-breadcrumb>

          <!-- Conteúdo da Página (Router Outlet) -->
          <div class="page-content">
            <router-outlet></router-outlet>
          </div>
        </main>
      </div>

      <!-- Footer -->
      <app-footer></app-footer>

      <!-- Loading Overlay Global -->
      <div class="loading-overlay" *ngIf="layoutService.globalLoading">
        <div class="spinner"></div>
      </div>
    </div>
  `,
  styleUrl: './main-layout.component.css'
})
export class MainLayoutComponent implements OnInit {
  private authService = inject(AuthService);
  private router = inject(Router);
  
  layoutService = inject(LayoutService);
  currentUser$ = this.authService.currentUser$;
  currentUser: any = null;

  ngOnInit() {
    // Observar mudanças de usuário
    this.currentUser$.subscribe(user => {
      this.currentUser = user;
    });

    // Observar mudanças de rota para atualizar breadcrumb
    this.router.events.pipe(
      filter(event => event instanceof NavigationEnd)
    ).subscribe((event: NavigationEnd) => {
      this.layoutService.updateCurrentRoute(event.url);
    });
  }

  onLogout() {
    this.authService.logout();
    this.router.navigate(['/auth/login']);
  }
}
```

### **layout/admin-layout/admin-layout.component.ts**

typescript

```typescript
import { Component, OnInit, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterOutlet } from '@angular/router';

// Layout Components específicos do Admin
import { AdminHeaderComponent } from '../admin-header/admin-header.component';
import { AdminSidebarComponent } from '../admin-sidebar/admin-sidebar.component';
import { AdminStatsBarComponent } from '../admin-stats-bar/admin-stats-bar.component';

// Services
import { AuthService } from '../../core/services/auth.service';
import { PermissionService } from '../../core/services/permission.service';

@Component({
  selector: 'app-admin-layout',
  standalone: true,
  imports: [
    CommonModule,
    RouterOutlet,
    AdminHeaderComponent,
    AdminSidebarComponent,
    AdminStatsBarComponent
  ],
  template: `
    <div class="admin-layout-container">
      <!-- Header Administrativo -->
      <app-admin-header 
        [currentUser]="currentUser"
        (logout)="onLogout()">
      </app-admin-header>

      <div class="admin-content-wrapper">
        <!-- Sidebar Admin com Permissões -->
        <app-admin-sidebar 
          [userPermissions]="userPermissions"
          [currentUser]="currentUser">
        </app-admin-sidebar>

        <!-- Área Principal Admin -->
        <div class="admin-main-content">
          <!-- Barra de Estatísticas -->
          <app-admin-stats-bar></app-admin-stats-bar>

          <!-- Conteúdo da Página Admin -->
          <section class="admin-page-content">
            <router-outlet></router-outlet>
          </section>
        </div>
      </div>

      <!-- Painel de Notificações Admin -->
      <div class="admin-notifications" *ngIf="hasAdminNotifications">
        <!-- Notificações específicas do admin -->
      </div>
    </div>
  `,
  styleUrl: './admin-layout.component.css'
})
export class AdminLayoutComponent implements OnInit {
  private authService = inject(AuthService);
  private permissionService = inject(PermissionService);

  currentUser: any = null;
  userPermissions: string[] = [];
  hasAdminNotifications = false;

  ngOnInit() {
    this.authService.currentUser$.subscribe(user => {
      this.currentUser = user;
      if (user) {
        this.loadUserPermissions(user.id);
      }
    });
  }

  private loadUserPermissions(userId: number) {
    this.permissionService.getUserPermissions(userId).subscribe(permissions => {
      this.userPermissions = permissions;
    });
  }

  onLogout() {
    this.authService.logout();
  }
}
```

### **layout/header/header.component.ts**

typescript

```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterLink } from '@angular/router';

@Component({
  selector: 'app-header',
  standalone: true,
  imports: [CommonModule, RouterLink],
  template: `
    <header class="main-header">
      <div class="header-left">
        <!-- Logo -->
        <div class="logo">
          <img src="/assets/images/logo.png" alt="Logo">
          <span class="brand-name">Sistema CRUD</span>
        </div>

        <!-- Menu Toggle -->
        <button 
          class="sidebar-toggle" 
          (click)="toggleSidebar.emit()"
          title="Toggle Menu">
          <i class="fas fa-bars"></i>
        </button>
      </div>

      <div class="header-center">
        <!-- Busca Global -->
        <div class="global-search">
          <input 
            type="text" 
            placeholder="Buscar..."
            class="search-input">
          <i class="fas fa-search search-icon"></i>
        </div>
      </div>

      <div class="header-right">
        <!-- Notificações -->
        <div class="notification-bell">
          <i class="fas fa-bell"></i>
          <span class="notification-badge">3</span>
        </div>

        <!-- Perfil do Usuário -->
        <div class="user-profile" *ngIf="currentUser">
          <img 
            [src]="currentUser.avatar || '/assets/images/default-avatar.png'" 
            [alt]="currentUser.name"
            class="user-avatar">
          <span class="user-name">{{ currentUser.name }}</span>
          
          <!-- Dropdown Menu -->
          <div class="user-dropdown">
            <a routerLink="/profile">
              <i class="fas fa-user"></i> Perfil
            </a>
            <a routerLink="/settings">
              <i class="fas fa-cog"></i> Configurações
            </a>
            <hr>
            <button (click)="logout.emit()">
              <i class="fas fa-sign-out-alt"></i> Sair
            </button>
          </div>
        </div>
      </div>
    </header>
  `,
  styleUrl: './header.component.css'
})
export class HeaderComponent {
  @Input() currentUser: any = null;
  @Output() toggleSidebar = new EventEmitter<void>();
  @Output() logout = new EventEmitter<void>();
}
```

### **layout/sidebar/sidebar.component.ts**

typescript

```typescript
import { Component, Input } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterLink, RouterLinkActive } from '@angular/router';

interface MenuItem {
  id: string;
  label: string;
  icon: string;
  route?: string;
  permission?: string;
  children?: MenuItem[];
  badge?: number;
}

@Component({
  selector: 'app-sidebar',
  standalone: true,
  imports: [CommonModule, RouterLink, RouterLinkActive],
  template: `
    <aside class="sidebar" [class.collapsed]="collapsed">
      <nav class="sidebar-nav">
        <ul class="menu-list">
          <li 
            *ngFor="let item of filteredMenuItems" 
            class="menu-item"
            [class.has-children]="item.children && item.children.length > 0">
            
            <!-- Menu Item Principal -->
            <a 
              *ngIf="item.route && !item.children"
              [routerLink]="item.route" 
              routerLinkActive="active"
              class="menu-link">
              <i [class]="item.icon"></i>
              <span class="menu-text" *ngIf="!collapsed">{{ item.label }}</span>
              <span class="menu-badge" *ngIf="item.badge && !collapsed">{{ item.badge }}</span>
            </a>

            <!-- Menu Item com Submenu -->
            <div 
              *ngIf="item.children && item.children.length > 0"
              class="menu-item-with-children">
              
              <button 
                class="menu-toggle"
                (click)="toggleSubmenu(item.id)">
                <i [class]="item.icon"></i>
                <span class="menu-text" *ngIf="!collapsed">{{ item.label }}</span>
                <i 
                  class="fas fa-chevron-down submenu-arrow" 
                  *ngIf="!collapsed"
                  [class.rotated]="isSubmenuOpen(item.id)">
                </i>
              </button>

              <!-- Submenu -->
              <ul 
                class="submenu" 
                *ngIf="!collapsed && isSubmenuOpen(item.id)">
                <li *ngFor="let child of item.children" class="submenu-item">
                  <a 
                    [routerLink]="child.route" 
                    routerLinkActive="active"
                    class="submenu-link">
                    <i [class]="child.icon"></i>
                    <span>{{ child.label }}</span>
                    <span class="menu-badge" *ngIf="child.badge">{{ child.badge }}</span>
                  </a>
                </li>
              </ul>
            </div>
          </li>
        </ul>

        <!-- Seção de Usuário no Sidebar -->
        <div class="sidebar-user" *ngIf="currentUser && !collapsed">
          <div class="user-info">
            <img 
              [src]="currentUser.avatar || '/assets/images/default-avatar.png'" 
              [alt]="currentUser.name"
              class="user-avatar-small">
            <div class="user-details">
              <span class="user-name">{{ currentUser.name }}</span>
              <span class="user-role">{{ currentUser.role }}</span>
            </div>
          </div>
        </div>
      </nav>
    </aside>
  `,
  styleUrl: './sidebar.component.css'
})
export class SidebarComponent {
  @Input() collapsed = false;
  @Input() currentUser: any = null;

  private openSubmenus = new Set<string>();

  menuItems: MenuItem[] = [
    {
      id: 'dashboard',
      label: 'Dashboard',
      icon: 'fas fa-home',
      route: '/dashboard'
    },
    {
      id: 'products',
      label: 'Produtos',
      icon: 'fas fa-box',
      children: [
        {
          id: 'products-list',
          label: 'Listar',
          icon: 'fas fa-list',
          route: '/products'
        },
        {
          id: 'products-create',
          label: 'Criar',
          icon: 'fas fa-plus',
          route: '/products/create'
        },
        {
          id: 'products-categories',
          label: 'Categorias',
          icon: 'fas fa-tags',
          route: '/products/categories'
        }
      ]
    },
    {
      id: 'orders',
      label: 'Pedidos',
      icon: 'fas fa-shopping-cart',
      badge: 5,
      children: [
        {
          id: 'orders-list',
          label: 'Todos os Pedidos',
          icon: 'fas fa-list',
          route: '/orders'
        },
        {
          id: 'orders-pending',
          label: 'Pendentes',
          icon: 'fas fa-clock',
          route: '/orders/pending',
          badge: 3
        },
        {
          id: 'orders-processing',
          label: 'Processando',
          icon: 'fas fa-cog',
          route: '/orders/processing',
          badge: 2
        }
      ]
    },
    {
      id: 'admin',
      label: 'Administração',
      icon: 'fas fa-cogs',
      permission: 'admin.access',
      children: [
        {
          id: 'admin-users',
          label: 'Usuários',
          icon: 'fas fa-users',
          route: '/admin/users',
          permission: 'users.manage'
        },
        {
          id: 'admin-analytics',
          label: 'Analytics',
          icon: 'fas fa-chart-bar',
          route: '/admin/analytics',
          permission: 'analytics.view'
        },
        {
          id: 'admin-reports',
          label: 'Relatórios',
          icon: 'fas fa-file-alt',
          route: '/admin/reports',
          permission: 'reports.view'
        }
      ]
    },
    {
      id: 'settings',
      label: 'Configurações',
      icon: 'fas fa-cog',
      route: '/settings'
    }
  ];

  get filteredMenuItems(): MenuItem[] {
    return this.menuItems.filter(item => this.hasPermission(item));
  }

  toggleSubmenu(itemId: string): void {
    if (this.openSubmenus.has(itemId)) {
      this.openSubmenus.delete(itemId);
    } else {
      this.openSubmenus.add(itemId);
    }
  }

  isSubmenuOpen(itemId: string): boolean {
    return this.openSubmenus.has(itemId);
  }

  private hasPermission(item: MenuItem): boolean {
    if (!item.permission) return true;
    
    // Verificar se o usuário tem a permissão necessária
    return this.currentUser?.permissions?.includes(item.permission) ?? false;
  }
}
```

### **layout/services/layout.service.ts**

typescript

```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class LayoutService {
  private sidebarCollapsedSubject = new BehaviorSubject<boolean>(false);
  private globalLoadingSubject = new BehaviorSubject<boolean>(false);
  private currentRouteSubject = new BehaviorSubject<string>('');

  public sidebarCollapsed$ = this.sidebarCollapsedSubject.asObservable();
  public globalLoading$ = this.globalLoadingSubject.asObservable();
  public currentRoute$ = this.currentRouteSubject.asObservable();

  get sidebarCollapsed(): boolean {
    return this.sidebarCollapsedSubject.value;
  }

  get globalLoading(): boolean {
    return this.globalLoadingSubject.value;
  }

  toggleSidebar(): void {
    const currentState = this.sidebarCollapsedSubject.value;
    this.sidebarCollapsedSubject.next(!currentState);
    
    // Salvar preferência no localStorage
    localStorage.setItem('sidebarCollapsed', (!currentState).toString());
  }

  setSidebarCollapsed(collapsed: boolean): void {
    this.sidebarCollapsedSubject.next(collapsed);
    localStorage.setItem('sidebarCollapsed', collapsed.toString());
  }

  setGlobalLoading(loading: boolean): void {
    this.globalLoadingSubject.next(loading);
  }

  updateCurrentRoute(route: string): void {
    this.currentRouteSubject.next(route);
  }

  // Carregar preferências salvas
  loadSavedPreferences(): void {
    const savedCollapsed = localStorage.getItem('sidebarCollapsed');
    if (savedCollapsed !== null) {
      this.sidebarCollapsedSubject.next(savedCollapsed === 'true');
    }
  }
}
```

## Como usar os Layouts nas Rotas:

### **app.routes.ts**

typescript

```typescript
export const routes: Routes = [
  // Layout Principal (Usuários comuns)
  {
    path: '',
    component: MainLayoutComponent,
    children: [
      { path: 'dashboard', loadComponent: () => import('./features/dashboard/dashboard.component').then(c => c.DashboardComponent) },
      { path: 'products', loadChildren: () => import('./features/products/product.routes').then(r => r.productRoutes) },
      { path: 'orders', loadChildren: () => import('./features/orders/order.routes').then(r => r.orderRoutes) }
    ]
  },
  
  // Layout Administrativo
  {
    path: 'admin',
    component: AdminLayoutComponent,
    canActivate: [authGuard, adminGuard],
    children: [
      { path: 'users', loadChildren: () => import('./features/users/user.routes').then(r => r.userRoutes) },
      { path: 'analytics', loadChildren: () => import('./features/analytics/analytics.routes').then(r => r.analyticsRoutes) }
    ]
  },

  // Layout de Autenticação (sem sidebar/header)
  {
    path: 'auth',
    component: AuthLayoutComponent,
    children: [
      { path: 'login', loadComponent: () => import('./features/auth/login/login.component').then(c => c.LoginComponent) },
      { path: 'register', loadComponent: () => import('./features/auth/register/register.component').then(c => c.RegisterComponent) }
    ]
  }
];
```

## Resumo da Camada Layout:

### **Responsabilidades:**

- **Estrutura visual comum** (header, sidebar, footer)
- **Navegação global** da aplicação
- **Gerenciamento de estado visual** (sidebar collapsed, loading global)
- **Layouts diferenciados** por tipo de usuário (user/admin)
- **Componentes de UI globais** (notificações, breadcrumb)

### **Vantagens:**

- **Reutilização** de estrutura visual
- **Consistência** na interface
- **Separação de responsabilidades** clara
- **Flexibilidade** para diferentes tipos de layout
- **Facilita manutenção** da interface global

A camada de Layout é essencial para manter consistência visual e separar as responsabilidades de estrutura da aplicação das funcionalidades específicas de cada feature.

## Organização em Camadas - Layout

### **Camada 1: Apresentação/UI (Layout)**

```
layout/
├── main-layout/           → Layout para usuários comuns
├── admin-layout/          → Layout para administradores  
├── auth-layout/           → Layout para autenticação
├── public-layout/         → Layout para páginas públicas
├── mobile-layout/         → Layout específico mobile
└── components/            → Componentes de layout
    ├── header/
    ├── sidebar/
    ├── footer/
    ├── breadcrumb/
    └── navigation/
```

### **Camada 2: Lógica de Negócio (Features)**

```
features/
├── products/              → Funcionalidade de produtos
├── users/                 → Gerenciamento de usuários
├── orders/                → Sistema de pedidos
├── dashboard/             → Painel principal
└── analytics/             → Relatórios e métricas
```

### **Camada 3: Dados/Serviços (Core)**

```
core/
├── services/              → Serviços de dados
├── guards/                → Proteção de rotas
├── interceptors/          → Interceptação HTTP
└── models/                → Modelos de dados
```

### **Camada 4: Infraestrutura (Shared)**

```
shared/
├── components/            → Componentes reutilizáveis
├── pipes/                 → Transformação de dados
├── directives/            → Diretivas customizadas
└── utils/                 → Utilitários gerais
```

---

## **Como as Camadas Interagem:**

### **Fluxo de Responsabilidade:**

**Layout (Apresentação)**

- Define ONDE as coisas aparecem
- Controla estrutura visual global
- Gerencia navegação entre features

⬇️

**Features (Negócio)**

- Define O QUE o usuário pode fazer
- Implementa regras de negócio específicas
- Usa componentes do layout para se apresentar

⬇️

**Core (Dados)**

- Define COMO os dados fluem
- Implementa comunicação com APIs
- Gerencia estado global da aplicação

⬇️

**Shared (Infraestrutura)**

- Define RECURSOS COMUNS
- Fornece ferramentas reutilizáveis
- Suporta todas as outras camadas

---

## **Hierarquia de Dependências:**

### **Layout depende de:**

- **Core**: Para autenticação, permissões, navegação
- **Shared**: Para componentes básicos (botões, ícones)

### **Features dependem de:**

- **Layout**: Para estrutura visual
- **Core**: Para serviços de dados
- **Shared**: Para componentes reutilizáveis

### **Core depende de:**

- **Shared**: Para utilitários básicos
- **Nada mais** (camada mais baixa)

### **Shared depende de:**

- **Nada** (camada de infraestrutura)

---

## **Separação de Responsabilidades:**

### **Layout é responsável por:**

- Estrutura visual (header, sidebar, footer)
- Navegação global
- Temas e estilos globais
- Responsividade da estrutura
- Estados visuais globais (loading, erros)

### **Layout NÃO é responsável por:**

- Lógica de negócio específica
- Manipulação de dados
- Validação de formulários
- Comunicação com APIs

---

## **Exemplo Prático - E-commerce:**

### **Rota: /products**

**1. Layout (main-layout)**

- Renderiza header com busca
- Mostra sidebar com categorias
- Define área de conteúdo principal

**2. Feature (products)**

- Lista produtos na área de conteúdo
- Implementa filtros e paginação
- Gerencia carrinho de compras

**3. Core (products.service)**

- Busca produtos na API
- Gerencia cache de produtos
- Controla estado dos produtos

**4. Shared (product-card)**

- Componente visual do produto
- Reutilizável em várias features
- Sem lógica de negócio

---

## **Rota: /admin/users**

**1. Layout (admin-layout)**

- Header com ferramentas admin
- Sidebar com opções administrativas
- Área de conteúdo com mais espaço

**2. Feature (users)**

- CRUD de usuários
- Gestão de permissões
- Relatórios de usuários

**3. Core (users.service)**

- API de gerenciamento de usuários
- Controle de permissões
- Auditoria de ações

**4. Shared (data-table)**

- Tabela reutilizável
- Paginação e ordenação
- Sem conhecimento de usuários

---

## **Benefícios da Organização em Camadas:**

### **Escalabilidade:**

- Cada camada pode crescer independentemente
- Novas features não afetam layout existente
- Layout pode ser redesenhado sem afetar funcionalidades

### **Manutenibilidade:**

- Mudanças isoladas por responsabilidade
- Fácil localização de problemas
- Testes focados por camada

### **Reutilização:**

- Layout reutilizado por múltiplas features
- Componentes shared usados em toda aplicação
- Serviços core compartilhados

### **Flexibilidade:**

- Diferentes layouts para diferentes contextos
- Features podem ser movidas entre layouts
- Componentes podem ser recombinados

A organização em camadas garante que cada parte da aplicação tenha uma responsabilidade clara e bem definida, facilitando desenvolvimento, manutenção e evolução do sistema.