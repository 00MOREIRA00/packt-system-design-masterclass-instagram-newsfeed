# Glossário Bilíngue (EN → PT-BR)

Termos técnicos recorrentes no case do Newsfeed, com a explicação de como cada um foi realmente usado no design (não definições genéricas de livro-texto).

### API Gateway

Porta de entrada única do sistema para requisições do cliente. No case, toda chamada (criar post, seguir usuário, curtir, comentar, ler o feed) chega primeiro ao API gateway, que a encaminha para o serviço correto (post writer service, follow service, comment service, like service, NewsFeedReader) através de um load balancer.

### Availability (Disponibilidade)

Requisito não funcional que expressa a fração do tempo em que o sistema deve estar no ar. No case, a meta definida é 99,999% de disponibilidade.

### Cache

Camada de memória rápida para evitar acessar o banco de dados a cada leitura. O case usa dois caches específicos: o **feeds cache**, que guarda o newsfeed já pré-computado de cada usuário (resultado do fan-out na escrita, para que a leitura seja uma busca direta), e o **likes cache**, que guarda o mapeamento post ID → contagem de curtidas. A estimativa de capacidade previu ~2 TB/dia de memória de cache (1% do armazenamento diário de 216 TB).

### CDN (Content Delivery Network)

Rede de distribuição de conteúdo usada para servir as imagens e vídeos dos posts. O newsfeed devolvido pelo NewsFeedReader contém só as URLs de mídia, não os arquivos — o cliente busca a imagem/vídeo real na CDN (e, se não estiver lá, no object storage). É por isso que, ao abrir o feed, o texto às vezes aparece antes da mídia.

### DAU / MAU (Usuários Ativos Diários / Mensais)

Duas das cinco métricas centrais da estimativa de capacidade. O case assume 500 milhões de DAU e 2 bilhões de MAU para o newsfeed — números usados depois para calcular throughput, storage e bandwidth (por exemplo, 10% dos DAU postando por dia gera os 50 milhões de escritas/dia).

### Encryption at Rest / in Transit (Encriptação em Repouso / em Trânsito)

Requisito de segurança discutido na simulação de entrevista: "em trânsito" significa TLS/HTTPS entre cliente e API gateway (e idealmente entre serviços internos e bancos de dados); "em repouso" significa habilitar encriptação nos cinco bancos do design (PostsDB, FeedsDB, CommentsDB, LikesDB, FollowDB) e no object storage — o S3, por exemplo, já suporta isso nativamente, inclusive nos uploads via pre-signed URL. A resposta-modelo sugere torná-lo mensurável (ex.: AES-256 em repouso, TLS 1.2+ em trânsito) em vez de deixá-lo vago.

### Endpoint

Caminho específico de uma API REST. O case usa endpoints como `/v1/posts` (criar post), `/v1/follow` (seguir usuário) e `/v1/feed/{userId}` (ler o feed), cada um combinando método HTTP + endpoint + body.

### Eventual Consistency (Consistência Eventual)

Requisito não funcional que aceita um pequeno atraso na propagação dos dados em troca de desempenho/escala. No case, é aceitável que um post leve cerca de 2 segundos para aparecer no feed dos seguidores, em vez de exigir atualização instantânea.

### Extensibility (Extensibilidade)

Requisito não funcional sobre a facilidade de adicionar features novas sem redesenhar o sistema — no case, exemplos citados são responder a um comentário (comentário aninhado) ou um sistema de recomendação de posts.

### Fan-out on Write (Fan-out na Escrita)

Estratégia central do high-level design: em vez de montar o feed no momento da leitura (join + sort caro), o sistema pré-computa o feed de cada seguidor no momento em que o post é criado. O post writer service publica um evento (postId, userId) numa message queue; o news feed generator service consome esse evento, busca os seguidores no FollowDB e grava o post no feed de cada um (FeedsDB + feeds cache). Troca escrita mais cara (uma gravação por seguidor) por leitura muito mais rápida — decisão justificada pela proporção de ~50 bilhões de leituras/dia contra ~50 milhões de escritas/dia.

### GraphDB (Banco de Dados de Grafo)

Tipo de banco escolhido para o FollowDB, porque o dado central ali é o relacionamento entre usuários (follower/followee). Usuários viram nós e a relação "segue" vira uma aresta direcionada, o que torna eficiente encontrar todos os seguidores (ou seguidos) de um usuário — consulta que o fan-out na escrita precisa fazer a cada novo post.

### Index (Índice)

Estrutura usada para acelerar a consulta mais comum de cada banco. No case, cada um dos cinco bancos é indexado pela chave mais consultada: PostsDB por post ID, FeedsDB e FollowDB por user ID, CommentsDB e LikesDB por post ID — sempre pensando no padrão de acesso, não no dado em si.

### Ingress / Egress

As duas direções do fluxo de rede (bandwidth) estimadas no capacity planning. No case, o ingress (dado entrando, equivalente ao volume armazenado por dia) foi calculado em ~2,5 GB/s, e o egress (dado saindo, isto é, sendo lido) em ~2,5 TB/s — cerca de 1000× maior, porque cada post é lido muito mais vezes do que é escrito. Esse desequilíbrio é o mesmo que justifica cache e CDN no high-level design.

### Latency (Latência)

Requisito não funcional sobre o tempo de resposta percebido pelo usuário. No case, a meta é o newsfeed carregar em 1 a 2 segundos ao abrir o app.

### Load Balancer (Balanceador de Carga)

Componente que distribui as requisições recebidas pelo API gateway entre as instâncias de um serviço (post writer service, follow service, comment service, like service, NewsFeedReader, pre-signed URL generator service), evitando sobrecarregar uma única instância.

### Message Queue (Fila de Mensagens)

Mecanismo assíncrono usado para desacoplar a escrita do processamento pesado. Usada em dois pontos do case: no fan-out na escrita (o post writer service publica {postId, userId}, e o news feed generator service consome para distribuir o post aos feeds dos seguidores) e no fluxo de curtir/comentar (o evento é enfileirado e o notification service, um serviço de terceiros, o consome para notificar o dono do post).

### NoSQL

Tipo de banco de dados escolhido para PostsDB, FeedsDB, CommentsDB e LikesDB. A escolha seguiu as diretrizes do case: dados sem estrutura fixa (posts podem ser só texto, só imagem ou só vídeo), escala muito alta (50 milhões de posts/dia, 1,5 bilhão de curtidas/comentários por dia) e padrão de consulta simples (buscar por post ID ou user ID, sem joins complexos) — cenário em que NoSQL escala horizontalmente melhor que SQL.

### Object Storage

Armazenamento usado para arquivos de mídia estáticos e volumosos (imagens e vídeos), separado dos bancos de dados principais. No fluxo de criação de post com mídia, o cliente sobe o arquivo direto no object storage via pre-signed URL, e só a URL resultante é salva no PostsDB — o dado pesado nunca passa pelo banco na hora da leitura.

### Pre-signed URL (URL Pré-Assinada)

URL temporária, gerada por um serviço dedicado (pre-signed URL generator service), que autoriza o cliente a fazer upload de mídia diretamente no object storage (ex.: Amazon S3) por um período limitado (o case cita ~10 minutos), sem passar pelo servidor de aplicação. Os dois motivos citados no deep dive: uploads mais rápidos (não sobrecarrega o servidor) e acesso seguro/temporário (a URL expira e deixa de funcionar depois do prazo).

### REST / RESTful API

Estilo de API usado em toda a comunicação cliente-servidor do case: criar post (`POST /v1/posts`), seguir usuário (`POST /v1/follow`), curtir/comentar e ler o feed (`GET /v1/feed/{userId}`) — cada chamada descrita como método HTTP + endpoint + body.

### Scalability (Escalabilidade)

Requisito não funcional que define quanta carga o sistema precisa aguentar. No case, a meta é suportar 500 milhões de usuários ativos diários e 2 bilhões de usuários ativos mensais — o número que, junto com a proporção leitura/escrita, guiou a escolha por fan-out na escrita e por bancos NoSQL/GraphDB.

### Throughput

Vazão — quantidade de requisições que o sistema processa por segundo (ou por dia). No case do Newsfeed, throughput de escrita (~50 milhões de posts/dia) e throughput de leitura (~50 bilhões de leituras de feed/dia) foram os números que guiaram a decisão de pré-computar o feed (fan-out na escrita) e explicam por que PostsDB, FeedsDB, CommentsDB e LikesDB são NoSQL e por que existe cache/CDN na leitura.

### Usability (Usabilidade)

Requisito não funcional sobre a qualidade percebida da experiência, independente de ser funcional ou rápido — no case, o exemplo citado é a renderização do post não deixar o texto aparecer sem a imagem/vídeo correspondente.
