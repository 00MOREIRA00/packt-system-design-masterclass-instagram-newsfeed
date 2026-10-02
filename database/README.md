# Bancos de Dados do Sistema de Newsfeed

O high-level design do case define cinco bancos de dados. A escolha entre NoSQL e GraphDB para cada um seguiu as diretrizes gerais de seleção de banco (velocidade de acesso, escala, estrutura dos dados, complexidade de consultas e evolução do schema) discutidas na seção "Seleção de Banco de Dados".

## Comparativo

| Banco | Estrutura / Schema | Escala | Escolha |
|---|---|---|---|
| PostsDB | Sem estrutura fixa | Alta (50M/dia) | **NoSQL** |
| FeedsDB | Não estruturado | Alta (50M/dia) | **NoSQL** |
| CommentsDB | Sem schema fixo (pode evoluir) | Muito alta (1,5B/dia) | **NoSQL** |
| LikesDB | Sem schema fixo (pode evoluir) | Muito alta (1,5B/dia) | **NoSQL** |
| FollowDB | Relacionamentos (grafo) | Alta (milhões de conexões) | **GraphDB** |

## Rationale por banco

**PostsDB.** Posts de redes sociais podem incluir texto, imagens, vídeo e metadados — alguns posts têm só texto, outros só imagem ou vídeo, ou seja, não há estrutura fixa. A escala é muito alta (50 milhões de requisições de criação de post por dia). E o padrão de consulta é simples: buscar um post pelo post ID, no fluxo de geração do newsfeed. Por não ter estrutura fixa, ter grande escala e consulta simples: escolha NoSQL.

**FeedsDB.** Armazena um mapeamento entre o user ID e o newsfeed dele. Como os posts não têm estrutura fixa, o FeedsDB também é naturalmente não estruturado, com a mesma alta escala (50 milhões/dia), e o padrão de consulta é simples: ler o newsfeed pelo user ID. Por isso: NoSQL também.

**CommentsDB.** Escala e throughput extremamente altos (1,5 bilhão de atividades por dia). Não tem schema fixo — os dados podem evoluir no futuro para incluir respostas a um comentário (comentários aninhados). O padrão de consulta mais comum é buscar todos os comentários de um post — simples, nada complexo. Escolha: NoSQL.

**LikesDB.** Bem parecido com o CommentsDB: 1,5 bilhão de atividades por dia. O schema também pode evoluir (diferentes tipos de reação, como "engraçado", "uau", "triste", "amei"). O padrão de consulta é simples: calcular o número de curtidas (para o likes cache) ou encontrar todos os usuários que curtiram um post. Escolha: NoSQL.

**FollowDB.** Armazena conexões e relacionamentos entre usuários (follower e followee). Podemos usar grafos para modelar isso: usuários como nós (nodes) e conexões como arestas (edges) — o que também ajuda a encontrar, de forma eficiente, a lista de seguidores e de seguidos de um usuário. Também é preciso suportar um grande número de usuários e relacionamentos (um usuário pode ter milhares ou milhões de seguidores). Escolha: GraphDB (banco de dados de grafo).

---

Cada banco também tem um arquivo `.schema.json` neste diretório com o modelo de campos (JSON Schema), incluindo qual campo é usado como índice (`x-indexed-by`):

- [`postsdb.schema.json`](./postsdb.schema.json)
- [`feedsdb.schema.json`](./feedsdb.schema.json)
- [`commentsdb.schema.json`](./commentsdb.schema.json)
- [`likesdb.schema.json`](./likesdb.schema.json)
- [`followdb.schema.json`](./followdb.schema.json)
