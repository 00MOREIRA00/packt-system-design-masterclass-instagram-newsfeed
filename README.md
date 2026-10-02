# System Design Masterclass — Notas de Estudo

Este repositório reúne minhas notas de estudo pessoais do curso **System Design Masterclass**, aplicadas a um estudo de caso completo: um sistema de Newsfeed nos moldes do Instagram (um feed de rede social). O material cobre todo o percurso de uma entrevista de system design — requisitos, estimativa de capacidade, design de API, arquitetura de alto nível e deep dive — além de perguntas de prática em estilo entrevista e um quiz de revisão para autoestudo.

## Estrutura do repositório

```
packt-system-design-masterclass-instagram-newsfeed/
├── README.md                        # este arquivo
├── glossario.md                     # glossário bilíngue (EN → PT-BR) dos termos técnicos do case
├── newsfeed.drawio                  # rascunho editável dos fluxos (abrir em app.diagrams.net)
├── docs/
│   ├── 01-requisitos.md             # requisitos funcionais e não funcionais do newsfeed
│   ├── 02-capacity-estimation.md    # estimativa de DAU/MAU, throughput, storage, cache e rede
│   ├── 03-api-design.md             # design das APIs REST (posts, curtidas, comentários, follow)
│   ├── 04-high-level-design.md      # arquitetura de alto nível e fluxos principais do sistema
│   ├── 05-deep-dive.md              # aprofundamento em bancos de dados, pre-signed URLs e mídia
│   ├── 06-simulacao-entrevista.md   # simulação de entrevista com perguntas e respostas-modelo
│   └── 07-quiz.md                   # quiz de revisão de múltipla escolha com gabarito comentado
├── diagrams/                        # diagramas referenciados nos docs
│   ├── *.png, *.dot                 # diagramas próprios (Graphviz), com legenda de cores/formas consistente
│   └── official_*.png               # diagramas originais do material do curso (uso pessoal de estudo)
└── database/
    ├── README.md                    # comparativo e rationale da escolha de cada banco de dados
    └── *.schema.json                # modelo de campos (JSON Schema) de cada banco
```

## Como estudar com isso

- **Leitura sequencial:** siga `docs/01-requisitos.md` até `docs/05-deep-dive.md` na ordem numérica para acompanhar o case completo do zero — do levantamento de requisitos até a arquitetura detalhada.
- **Autoteste:** use `docs/07-quiz.md` para revisão rápida; cada questão vem com a resposta escondida em um bloco expansível, para testar o conhecimento antes de conferir o gabarito.
- **Prática oral de entrevista:** use `docs/06-simulacao-entrevista.md` para treinar respostas faladas — leia só a pergunta do entrevistador em cada bloco, tente responder em voz alta, e só depois compare com a resposta-modelo.
- **Consulta rápida:** use `glossario.md` sempre que um termo técnico (throughput, fan-out, GraphDB, pre-signed URL etc.) aparecer sem contexto suficiente, e `database/README.md` para revisar rapidamente por que cada banco (PostsDB, FeedsDB, CommentsDB, LikesDB, FollowDB) foi escolhido como NoSQL ou GraphDB.
