MODULO-[NOME]/
statenode/
├── pom.xml                              ← PAI

├── statenode-core/
│   └── src/main/java/br/com/statenode/
│       └── domain/
│           ├── entities/                ← Processo, ProcessoSchema, WorkflowBlueprint
│           ├── valueobjects/            ← Contexto, SlotPayload, TipoSlot, StatusProcesso
│           ├── events/                  ← SlotPreenchidoEvent, WorkflowIniciado...
│           ├── exceptions/              ← SchemaVioladoException, ProcessoException...
│           └── gateways/               ← IProcessoRepository, IWorkflowEnginePort...

├── statenode-application/
│   └── src/main/java/br/com/statenode/
│       └── application/
│           └── usecases/
│               ├── CriarProcesso/
│               │   ├── Interactor.java
│               │   ├── InputPort.java
│               │   └── OutputPort.java
│               ├── PreencherSlot/
│               ├── IniciarWorkflow/
│               └── ValidarBlueprint/

├── statenode-infrastructure/
│   └── src/main/java/br/com/statenode/
│       └── infrastructure/
│           ├── persistence/
│           │   ├── entities/            ← ProcessoJpaEntity, SchemaJpaEntity...
│           │   ├── repositories/        ← ProcessoJpaRepository (Spring Data)
│           │   └── adapters/            ← ContextoJsonbConverter, ProcessoRepositoryImpl
│           ├── temporal/
│           │   ├── DynamicLegalWorkflow.java
│           │   ├── DynamicLegalWorkflowImpl.java
│           │   └── activities/          ← RegistrarAndamento, EnviarNotificacao...
│           ├── events/
│           │   ├── SpringEventPublisherAdapter.java
│           │   └── listeners/           ← SlotPreenchidoListener, WorkflowIniciadoListener
│           └── adapters/               ← TemporalWorkflowEngineAdapter
└── statenode-webapi/
    └── src/main/java/br/com/statenode/
        ├── StateNodeApplication.java
        ├── webapi/
        │   ├── entrypoints/             ← ProcessoController
        │   ├── listeners/               ← (reservado para consumidores externos futuros)
        │   └── dtos/                    ← CriarProcessoRequest, PreencherSlotRequest
        └── config/                      ← TemporalConfig, JacksonConfig
