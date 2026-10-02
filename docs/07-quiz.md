# Quiz de Revisão: Capacity Estimation, API e Arquitetura

Um quiz de múltipla escolha para revisão rápida, cobrindo capacity estimation, presigned URLs e o fluxo de notificações do sistema de newsfeed — os mesmos temas já documentados nas seções anteriores. A resposta correta fica escondida em um bloco expansível ("Ver resposta"), seguida de uma breve justificativa.

### Questão 1. Por que a estimativa de capacidade é importante antes de escolher um banco de dados para sistemas de rede social?

- a) Remove a necessidade de backups
- b) Ajuda a escolher as cores da interface do usuário
- c) Torna as features mais fáceis de codificar
- d) Garante que o sistema aguente o volume de consultas e de dados necessário

<details>
<summary>Ver resposta</summary>

**Resposta correta: d) Garante que o sistema aguente o volume de consultas e de dados necessário**

Como vimos na Seleção de Banco de Dados, a escala (50 milhões/dia, 1,5 bilhão/dia) foi um dos critérios decisivos para escolher NoSQL vs. GraphDB — é a capacity estimation que revela esses números antes da escolha do banco.

</details>

### Questão 2. Qual é o uso principal de uma presigned URL no contexto de upload de mídia para o object storage?

- a) Conceder acesso ilimitado ao armazenamento
- b) Permitir que o cliente faça upload de arquivos de mídia diretamente no object storage
- c) Enviar notificações
- d) Autenticar o login do usuário

<details>
<summary>Ver resposta</summary>

**Resposta correta: b) Permitir que o cliente faça upload de arquivos de mídia diretamente no object storage**

É exatamente a definição que documentamos no Deep Dive: a pre-signed URL dá ao cliente permissão temporária para fazer upload direto no object storage, sem passar pelo servidor.

</details>

### Questão 3. Por que é importante estimar o número de read requests no planejamento de um sistema de rede social?

- a) Para garantir que o sistema aguente cargas de tráfego altas de forma eficiente
- b) Para decidir algoritmos de encriptação
- c) Para definir políticas de senha do usuário
- d) Para determinar o formato de armazenamento dos posts

<details>
<summary>Ver resposta</summary>

**Resposta correta: a) Para garantir que o sistema aguente cargas de tráfego altas de forma eficiente**

Conecta direto com o throughput de leitura calculado (50 bilhões de leituras/dia) — foi esse número que justificou o fan-out na escrita e o feeds cache no high-level design.

</details>

### Questão 4. Quando um usuário comenta em um post, qual serviço tipicamente lida com a notificação ao dono do post?

- a) Post Writer Service
- b) API Gateway
- c) Notification service
- d) Comment database

<details>
<summary>Ver resposta</summary>

**Resposta correta: c) Notification service**

No fluxo de HLD "Comentar em um Post": o comment service salva no CommentsDB e coloca um evento na message queue; o notification service (serviço de terceiros) retira esse evento da fila e notifica o dono do post.

</details>

### Questão 5. Qual das opções abaixo NÃO é tipicamente estimada durante o capacity planning de sistemas de rede social?

- a) Usuários ativos diários (DAU)
- b) Throughput
- c) Usuários ativos mensais (MAU)
- d) Esquema de cores dos elementos de UI

<details>
<summary>Ver resposta</summary>

**Resposta correta: d) Esquema de cores dos elementos de UI**

DAU/MAU e throughput são 2 dos 5 pilares da estimativa de capacidade que vimos (junto de storage, memória/cache e rede/bandwidth); esquema de cores de interface não tem relação nenhuma com capacity planning.

</details>
