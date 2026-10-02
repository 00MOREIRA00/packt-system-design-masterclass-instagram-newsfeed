<!-- Parte do case: Sistema de Newsfeed (Instagram) — System Design Masterclass -->

# Requisitos Funcionais e Não Funcionais

## Requisitos Funcionais

Ao fazer um system design, o primeiro passo é definir os requisitos. Aqui existem basicamente duas partes: a primeira é definir os requisitos funcionais, e a segunda é definir os requisitos não funcionais. Vamos ver a primeira, que são os requisitos funcionais.

Os requisitos funcionais basicamente falam sobre as funcionalidades. Nesse caso, para o nosso sistema de newsfeed, temos principalmente cinco requisitos.

**1. Criar posts de rede social.** Os usuários têm o poder de criar um post de rede social. O post pode ser um post de texto, de imagem ou de vídeo.

**2. Seguir e deixar de seguir outros usuários.** Os usuários têm a capacidade de seguir ou deixar de seguir outro usuário.

**3. Newsfeed (feed de notícias).** Os usuários podem visualizar seu newsfeed. O feed mostra os posts dos usuários que eles seguem, em ordem cronológica reversa — do mais novo para o mais antigo.

**4. Curtir e comentar.** Os usuários podem curtir ou comentar nos posts de outras pessoas.

**5. Notificação do usuário.** Os usuários são notificados quando outro usuário curte ou comenta em seu post.

## Requisitos Não Funcionais

Agora vamos passar para os requisitos não funcionais. Os requisitos não funcionais basicamente falam de coisas como disponibilidade (availability) e escalabilidade (scalability), que não estão nos requisitos funcionais — não são as funcionalidades principais que buscamos no nosso sistema. Claro, queremos disponibilidade e escalabilidade, mas em que medida (métrica)? É isso que discutimos nos requisitos não funcionais. No nosso sistema de newsfeed, vamos discutir seis deles.

**1. Disponibilidade (availability).** Sem dúvida queremos que o nosso sistema de newsfeed esteja disponível a maior parte do tempo, certo? Mas aqui vamos definir uma métrica: vamos estabelecer que o sistema fica no ar (up) 99,999% do tempo.

**2. Consistência eventual (eventual consistency).** Se um usuário posta algo, tudo bem que isso apareça dois segundos depois, ou algo assim. Não queremos que seja instantâneo — bom, idealmente até gostaríamos, seria uma experiência de usuário maravilhosa —, mas está tudo bem ter uns dois segundos de atraso.

**3. Latência (latency).** Queremos que o nosso sistema tenha baixa latência. O que isso quer dizer? Quando clicamos no botão home, o newsfeed deve carregar em um a dois segundos, e não mais do que isso.

**4. Escalabilidade (scalability).** Instagram ou Twitter, se você reparar, estão escalados pelo mundo todo, e é isso que queremos aqui. Nosso sistema de newsfeed deve suportar 500 milhões de usuários ativos diários e 2 bilhões de usuários ativos mensais. Se você ficou totalmente confuso sobre como chegamos a esses números, pode simplesmente pesquisar no Google.

**5. Extensibilidade (extensibility).** Todo mundo quer projetar um sistema que seja bastante extensível. Em um sistema de newsfeed, por exemplo, queremos projetar o sistema de forma que seja fácil introduzir funcionalidades como responder a um comentário ou um sistema de recomendação de posts.

**6. Usabilidade (usability).** Para o nosso sistema de newsfeed, a renderização deve ser super rápida. O que isso quer dizer? Bem, é bem frustrante ver um post carregar o texto mas não a imagem ou o vídeo, certo? Isso já aconteceu com todos nós, e não gostamos disso. Para os nossos usuários, queremos uma boa experiência, e é por isso que precisamos dessa usabilidade.

Bem, esses seis pontos concluem a nossa seção de requisitos não funcionais.

> **Resumo rápido — pontos-chave para a entrevista**
>
> - Funcionais: criar post (texto/imagem/vídeo), seguir/deixar de seguir, ver o newsfeed (ordem cronológica reversa), curtir/comentar, notificar o dono do post.
> - Não funcionais: alta disponibilidade (99,999%), consistência eventual (~2s de atraso é aceitável), baixa latência (feed carrega em 1–2s), alta escalabilidade (500M DAU / 2B MAU), extensibilidade e boa usabilidade (renderização rápida de mídia).
> - Dica de entrevista: sempre alinhe com o entrevistador o número de usuários e o nível de consistência assumidos — são suposições, não fatos, e o entrevistador quer ver você negociando o escopo.
