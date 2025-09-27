### Estrutura de Pastas Module

```
my-ecommerce-app/
├── src/
│   ├── app/
│   │   ├── core/
│   │   │   ├── services/
│   │   │   │   ├── api.service.ts
│   │   │   │   └── auth.service.ts
│   │   │   ├── components/
│   │   │   │   └── header/
│   │   │   │       ├── header.component.html
│   │   │   │       ├── header.component.css
│   │   │   │       └── header.component.ts
│   │   │   ├── guards/
│   │   │   │   └── auth.guard.ts
│   │   │   ├── interceptors/
│   │   │   │   └── auth.interceptor.ts
│   │   │   └── core.module.ts
│   │   │
│   │   ├── shared/
│   │   │   ├── components/
│   │   │   │   ├── card/
│   │   │   │   │   └── card.component.ts
│   │   │   │   └── button/
│   │   │   │       └── button.component.ts
│   │   │   ├── pipes/
│   │   │   │   └── currency.pipe.ts
│   │   │   └── shared.module.ts
│   │   │
│   │   ├── features/
│   │   │   ├── home/
│   │   │   │   ├── home.component.html
│   │   │   │   ├── home.component.ts
│   │   │   │   └── home.module.ts
│   │   │   │
│   │   │   └── products/
│   │   │       ├── product-list/
│   │   │       │   ├── product-list.component.html
│   │   │       │   ├── product-list.component.ts
│   │   │       │   └── product-list.component.css
│   │   │       ├── product-detail/
│   │   │       │   ├── product-detail.component.html
│   │   │       │   ├── product-detail.component.ts
│   │   │       │   └── product-detail.component.css
│   │   │       ├── services/
│   │   │       │   └── product.service.ts
│   │   │       └── products.module.ts
│   │   │
│   │   ├── app-routing.module.ts
│   │   └── app.module.ts
│   └── main.ts
└── ...
```

---


### Camadas e Organização

Essa estrutura é dividida em camadas lógicas para isolar responsabilidades e promover a reutilização.

#### **1. Camada Principal: `App`**

Esta é a camada raiz da sua aplicação.

- `app.module.ts`: O módulo raiz, onde você importa todos os outros módulos principais.
    
- `app-routing.module.ts`: O roteamento principal da aplicação, responsável por carregar os módulos de recursos (features) de forma preguiçosa (lazy loading).
    

#### **2. Camada de Recurso: `Features`**

Cada pasta dentro de `features` representa um recurso ou uma funcionalidade específica da sua aplicação, como `home`, `products`, `checkout`, `profile`, etc.

- **Isolamento**: O código de um recurso (`products`) fica totalmente contido em sua própria pasta. Isso evita que a lógica de `products` se misture com a lógica de `home`.
    
- **Lazy Loading**: Os módulos de recursos (`products.module.ts`) são geralmente carregados de forma preguiçosa para melhorar o desempenho inicial da aplicação.
    
- **Estrutura interna**: Cada módulo de recurso pode ter seus próprios componentes, serviços, rotas e até sub-módulos. Por exemplo, em `products`, você pode ter componentes como `product-list` e `product-detail` e um serviço específico (`product.service.ts`) para lidar com a lógica de produtos.
    

#### **3. Camada de Serviço: `Core`**

O **`CoreModule`** contém os serviços e componentes que serão usados apenas uma vez em toda a aplicação. Ele é importado apenas no `AppModule` e nunca em outros módulos.

- **Serviços globais**: Serviços como `AuthService` para autenticação ou `ApiService` para comunicação HTTP.
    
- **Guards e Interceptors**: `AuthGuard` para proteger rotas e `AuthInterceptor` para adicionar um token de autenticação em todas as requisições.
    
- **Componentes de layout**: Componentes como `HeaderComponent` e `FooterComponent` que fazem parte do layout principal da aplicação.
    

#### **4. Camada de Compartilhamento: `Shared`**

O **`SharedModule`** contém os componentes, pipes e diretivas que serão usados em **múltiplos módulos de recurso**. Ele deve ser importado em todos os módulos que precisam de suas funcionalidades.

- **Componentes reutilizáveis**: Pequenos componentes de UI que podem ser usados em vários lugares, como um `CardComponent` para exibir produtos ou um `ButtonComponent`. Esses componentes não devem ter lógica de negócio e apenas receber e emitir dados.
    
- **Pipes e Diretivas**: Lógica de formatação de dados (como `CurrencyPipe`) e diretivas que modificam o DOM.
    
- **Módulo genérico**: O `SharedModule` não deve ter serviços e não deve ser dependente de nenhum outro módulo de recurso, o que o torna totalmente reutilizável.
    

### Reutilização de Componentes

Nessa estrutura, a reutilização de componentes é simples e direta:

1. **Componentes Reutilizáveis**: Crie um componente como `CardComponent` dentro da pasta `shared/components`.
    
2. **Exportar**: Exporte o `CardComponent` no `SharedModule`.
    
3. **Importar**: Importe o `SharedModule` em qualquer módulo de recurso que precise usar o `CardComponent` (por exemplo, no `ProductsModule` e no `HomeModule`).
    

Dessa forma, o `CardComponent` pode ser usado em diferentes partes do sistema sem a necessidade de reescrever o código, mantendo a consistência visual e a DRY (Don't Repeat Yourself - Não se Repita).

## **`features` (Recomendado)**

**Mais apropriado quando:**

- Você pensa em **funcionalidades** do sistema
- Cada pasta representa uma **área de negócio** completa
- Contém lógica complexa, serviços, guards, etc.

```
features/
├── user-management/     # Funcionalidade completa
├── product-catalog/     # Área de negócio
├── order-processing/    # Fluxo de trabalho
└── dashboard/           # Conjunto de recursos
```

## **`pages` (Alternativa válida)**

**Mais apropriado quando:**

- Você pensa em **rotas/páginas** da aplicação
- Cada pasta representa uma **tela específica**
- Foco na estrutura de navegação

```
pages/
├── home/               # Página inicial
├── product-list/       # Página de listagem
├── product-detail/     # Página de detalhes
└── checkout/           # Página de checkout
```

## **Recomendação: `features`**

O nome `features` é mais semântico porque:

1. **Melhor abstração**: Uma feature pode conter múltiplas páginas
2. **Escalabilidade**: Permite agrupar funcionalidades relacionadas
3. **Padrão da comunidade**: Mais comum na documentação oficial e style guides
4. **Flexibilidade**: Funciona bem com lazy loading de módulos

## **Estrutura Híbrida (Opção avançada)**

Alguns projetos usam ambos:

```
src/app/
├── features/           # Funcionalidades complexas
│   ├── user-management/
│   └── order-processing/
└── pages/              # Páginas simples
    ├── about/
    └── contact/
```

**Conclusão**: Use `features` como padrão, especialmente em aplicações médias/grandes onde você terá funcionalidades complexas com múltiplos componentes, serviços e lógica de negócio.