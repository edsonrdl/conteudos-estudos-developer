# 📚 Guia de Estudos: Angular
#tag #frontend #frameworks #angular

> [!info] Visão Geral
> Angular é um framework front-end mantido pelo Google para construção de aplicações web robustas e escaláveis. Este guia cobre desde a configuração do ambiente e os fundamentos de componentes até roteamento, gerenciamento de estado com RxJS/NgRx, testes, performance e as novidades das versões 17, 18 e 19.

---

## 1 Domínio: Fundamentos e Ambiente

### 1.1 Introdução ao Angular
- **1.1.1. O que é o Angular**
  - 1.1.1.1. História e evolução do framework.
  - 1.1.1.2. Diferenças entre AngularJS e Angular.
  - 1.1.1.3. Principais marcos da evolução do Angular.

### 1.2 Configuração do Ambiente
- **1.2.1. Instalação**
  - 1.2.1.1. Instalação do Node.js e npm.
  - 1.2.1.2. Angular CLI — instalação e comandos básicos.

### 1.3 Criação de um Novo Projeto
- **1.3.1. Estrutura do Projeto**
  - 1.3.1.1. Estrutura de pastas e arquivos gerados pelo CLI.
  - 1.3.1.2. Comandos básicos do Angular CLI.

---

## 2 Domínio: Componentes e Templates

### 2.1 Componentes
- **2.1.1. Criação de Componentes**
  - 2.1.1.1. Comando para gerar componentes via CLI.
  - 2.1.1.2. Estrutura de um componente — template, estilos e classe.
- **2.1.2. Interação entre Componentes**
  - 2.1.2.1. @Input e @Output.
  - 2.1.2.2. ViewChild e ContentChild.
- **2.1.3. Ciclo de Vida dos Componentes**
  - 2.1.3.1. ngOnInit.
  - 2.1.3.2. ngOnChanges.
  - 2.1.3.3. ngOnDestroy.

### 2.2 Templates e Diretivas
- **2.2.1. Templates**
  - 2.2.1.1. Sintaxe de interpolação.
  - 2.2.1.2. Bindings — property binding e event binding.
- **2.2.2. Diretivas Estruturais**
  - 2.2.2.1. *ngIf.
  - 2.2.2.2. *ngFor.
  - 2.2.2.3. *ngSwitch.
- **2.2.3. Diretivas de Atributo**
  - 2.2.3.1. ngClass e ngStyle.
  - 2.2.3.2. Criação de diretivas customizadas.

---

## 3 Domínio: Módulos e Roteamento

### 3.1 Módulos
- **3.1.1. Módulos no Angular**
  - 3.1.1.1. NgModule e estrutura de módulos.
  - 3.1.1.2. Módulo raiz e módulos secundários.
- **3.1.2. Carregamento Tardio (Lazy Loading)**
  - 3.1.2.1. Configuração de lazy loading.
  - 3.1.2.2. Benefícios do lazy loading.

### 3.2 Roteamento e Navegação
- **3.2.1. Configuração de Roteamento**
  - 3.2.1.1. Configurando rotas no Angular.
  - 3.2.1.2. RouterLink e RouterOutlet.
- **3.2.2. Rotas Protegidas (Guards)**
  - 3.2.2.1. CanActivate e CanDeactivate.
  - 3.2.2.2. Resolvers.

---

## 4 Domínio: Serviços e Dados

### 4.1 Serviços e Injeção de Dependência
- **4.1.1. Criação de Serviços**
  - 4.1.1.1. Serviço singleton.
  - 4.1.1.2. Injeção de dependência.
- **4.1.2. HTTP Client**
  - 4.1.2.1. Realizando requisições HTTP.
  - 4.1.2.2. Observables e Promises.
- **4.1.3. Interceptors**
  - 4.1.3.1. Criação de interceptors.
  - 4.1.3.2. Usos comuns — autenticação e logging.

### 4.2 Formulários
- **4.2.1. Formulários Reativos**
  - 4.2.1.1. FormGroup e FormControl.
  - 4.2.1.2. Validações reativas.
- **4.2.2. Formulários Baseados em Template**
  - 4.2.2.1. Template-driven forms.
  - 4.2.2.2. Validações baseadas em template.

### 4.3 Pipes
- **4.3.1. Uso de Pipes**
  - 4.3.1.1. Pipes embutidos — Date, Currency, Decimal.
  - 4.3.1.2. Criação de pipes customizados.

### 4.4 Observables e RxJS
- **4.4.1. Introdução ao RxJS**
  - 4.4.1.1. Observables vs Promises.
  - 4.4.1.2. Operadores comuns — map, filter, mergeMap.
- **4.4.2. Gerenciamento de Estado**
  - 4.4.2.1. BehaviorSubject e ReplaySubject.
  - 4.4.2.2. Uso de NgRx.

---

## 5 Domínio: Qualidade e Internacionalização

### 5.1 Testes
- **5.1.1. Testes Unitários**
  - 5.1.1.1. Introdução ao Jasmine e Karma.
  - 5.1.1.2. Testes de componentes e serviços.
- **5.1.2. Testes de Integração**
  - 5.1.2.1. TestBed e configuração de testes.
  - 5.1.2.2. Testes com HttpClient e rotas.

### 5.2 Internacionalização (i18n)
- **5.2.1. Configuração de i18n**
  - 5.2.1.1. Módulo de internacionalização do Angular.
  - 5.2.1.2. Tradução de templates e strings.

### 5.3 Melhores Práticas e Performance
- **5.3.1. Arquitetura e Padrões de Projeto**
  - 5.3.1.1. Estrutura recomendada de projetos Angular.
  - 5.3.1.2. Design Patterns comuns — MVC e MVVM.
- **5.3.2. Otimização de Performance**
  - 5.3.2.1. Change Detection Strategies.
  - 5.3.2.2. Uso do trackBy em *ngFor.
  - 5.3.2.3. Angular Universal — renderização no lado do servidor.

---

## 6 Domínio: Evolução e Ecossistema

### 6.1 Novidades por Versão
- **6.1.1. Versão 17**
  - 6.1.1.1. Standalone Components.
  - 6.1.1.2. Hydration avançado para SSR.
  - 6.1.1.3. Control Flow Syntax — @if, @for, @switch.
  - 6.1.1.4. Deferrable Views para carregamento dinâmico.
- **6.1.2. Versão 18**
  - 6.1.2.1. Introdução ao Signals para reatividade simplificada.
  - 6.1.2.2. Melhorias de performance e hydration.
  - 6.1.2.3. Zone.js opcional.
  - 6.1.2.4. DevTools aprimoradas.
- **6.1.3. Versão 19**
  - 6.1.3.1. APIs aprimoradas para integração com bibliotecas externas.
  - 6.1.3.2. Dynamic Imports aprimorados.
  - 6.1.3.3. Reatividade otimizada com Signals.
  - 6.1.3.4. Reduções significativas no tamanho dos bundles.
  - 6.1.3.5. Melhorias na interoperabilidade com bibliotecas modernas.
  - 6.1.3.6. Ferramentas CLI aprimoradas.

### 6.2 Ferramentas e Bibliotecas Complementares
- **6.2.1. Angular Material**
  - 6.2.1.1. Configuração e uso de Angular Material.
  - 6.2.1.2. Componentes comuns — mat-table, mat-form-field.
- **6.2.2. Outras Bibliotecas Úteis**
  - 6.2.2.1. NgRx para gerenciamento de estado.
  - 6.2.2.2. Angular Flex-Layout para design responsivo.

### 6.3 Prática e Projetos
- **6.3.1. Exercícios Práticos**
  - 6.3.1.1. Criar um aplicativo de lista de tarefas (to-do list).
  - 6.3.1.2. Desenvolver uma aplicação de e-commerce com carrinho de compras.
- **6.3.2. Projetos Avançados**
  - 6.3.2.1. Aplicações de chat em tempo real com WebSockets.
  - 6.3.2.2. Integração com Firebase para backend e autenticação.

---

> **Links Relacionados:**
> HTML5
> CSS
> JavaScript
> TypeScript
> RxJS
> VueJS
> React
