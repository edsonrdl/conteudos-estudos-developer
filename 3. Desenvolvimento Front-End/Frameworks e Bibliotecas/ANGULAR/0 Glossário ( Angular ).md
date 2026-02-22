
## Angular 19 

1. Introdução ao Angular

1.1. O que é o Angular?

1.1.1. História e evolução

1.1.2. Diferenças entre AngularJS e Angular

1.1.3. Principais diferenças da evolução do angular

1.2. Configuração do Ambiente

1.2.1. Instalação do Node.js e npm

1.2.2. Angular CLI: instalação e comandos básicos

1.3. Criação de um Novo Projeto

1.3.1. Estrutura do projeto Angular

1.3.2. Comandos básicos do Angular CLI

2. Componentes

2.1. Criação de Componentes

2.1.1. Comando para gerar componentes

2.1.2. Estrutura de um componente (template, estilos, e classe)

2.2. Interação entre Componentes

2.2.1. @Input e @Output

2.2.2. ViewChild e ContentChild

2.3. Métodos  de ciclo de Vida dos Componentes

2.3.1.  ngOnInit

2.3.2. ngOnChanges

2.3.3. ngOnDestroy

3. Templates e Diretivas

3.1. Templates

3.1.1. Sintaxe de interpolação

3.1.2. Bindings: property e event bindings

3.2. Diretivas Estruturais

3.2.1. *ngIf,

3.2.2.  *ngFor

3.2.3.*ngSwitch

3.3. Diretivas de Atributo

3.3.1. ngClass, ngStyle

3.3.2. Criação de diretivas customizadas

4. Módulos

4.1. Módulos no Angular

4.1.1. NgModule e estrutura de módulos

4.1.2. Módulo raiz e módulos secundários

4.2. Carregamento Tardio (Lazy Loading)

4.2.1. Configuração de lazy loading

4.2.2. Benefícios do lazy loading

5. Serviços e Injeção de Dependência

5.1. Criação de Serviços

5.1.1. Serviço singleton

5.1.2. Injeção de dependência

5.2. HTTP Client

5.2.1. Realizando requisições HTTP

5.2.2. Observables e Promises

5.3. Interceptors

5.3.1. Criação de interceptors

5.3.2. Usos comuns (ex.: autenticação, logging)

6. Roteamento e Navegação

6.1. Configuração de Roteamento

6.1.1. Configurando rotas no Angular

6.1.2. RouterLink e RouterOutlet

6.2. Rotas Protegidas (Guards)

6.2.1. CanActivate, CanDeactivate

6.2.2. Resolvers

7. Formulários

7.1. Formulários Reativos

7.1.1. FormGroup e FormControl

7.1.2. Validações reativas

7.2. Formulários Baseados em Template

7.2.1. Template-driven forms

7.2.2. Validações baseadas em template

8. Pipes

8.1. Uso de Pipes no Angular

8.1.1. Pipes embutidos (ex.: Date, Currency, Decimal)

8.1.2. Criação de pipes customizados

9. Observables e RxJS

9.1. Introdução ao RxJS

9.1.1. Observables vs Promises

9.1.2. Operadores comuns (map, filter, mergeMap, etc.)

9.2. Gerenciamento de Estado

9.2.1. BehaviorSubject e ReplaySubject

9.2.2. Uso de NgRx

10. Internacionalização (i18n)

10.1. Configuração de i18n

10.1.1. Módulo de internacionalização do Angular

10.1.2. Tradução de templates e strings

11. Testes

11.1. Testes Unitários

11.1.1. Introdução ao Jasmine e Karma

11.1.2. Testes de componentes e serviços

11.2. Testes de Integração

11.2.1. TestBed e configuração de testes

11.2.2. Testes com HttpClient e rotas

12. Melhores Práticas e Performance

12.1. Arquitetura e Padrões de Projeto

12.1.1. Estrutura recomendada de projetos Angular

12.1.2. Design Patterns comuns (ex.: MVC, MVVM)

12.2. Otimização de Performance

12.2.1. Change Detection Strategies

12.2.2. Uso do trackBy em *ngFor

12.2.3. Angular Universal (renderização no lado do servidor)

13. Atualizações e Novidades

13.1. Novidades na Versão 17

13.1.1. StandaloneComponents

13.1.2. Hydration avançado para SSR

13.1.3. Control Flow Syntax: @if, @for, @switch

13.1.4. Deferrable Views para carregamento dinâmico

13.2. Novidades na Versão 18

13.2.1. Introdução ao Signals para reatividade simplificada

13.2.2. Melhorias de performance e hydration

13.2.3. Zone.js opcional

13.2.4. DevTools aprimoradas

13.3. Novidades na Versão 19

13.3.1. APIs aprimoradas para integração com bibliotecas externas

13.3.2. Dynamic Imports aprimorados

13.3.3. Reatividade otimizada com Signals

13.3.4. Reduções significativas no tamanho dos bundles

13.3.5. Melhorias na interoperabilidade com bibliotecas modernas

13.3.6. Ferramentas CLI aprimoradas

14. Ferramentas e Bibliotecas Complementares

14.1. Angular Material

14.1.1. Configuração e uso de Angular Material

14.1.2. Componentes comuns: mat-table, mat-form-field, etc.

14.2. Outras Bibliotecas Úteis

14.2.1. NgRx para gerenciamento de estado

14.2.2. Angular Flex-Layout para design responsivo

15. Prática e Projetos

15.1. Exercícios Práticos

15.1.1. Criar um aplicativo de lista de tarefas (to-do list)

15.1.2. Desenvolver uma aplicação de e-commerce com carrinho de compras

15.2. Projetos Avançados

15.2.1. Aplicações de chat em tempo real com WebSockets

15.2.2. Integração com Firebase para backend e autenticação