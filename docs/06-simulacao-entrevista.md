# Simulação de Entrevista: Requisitos do Sistema de Newsfeed

Esta seção reúne uma simulação de entrevista de system design focada em requisitos, praticada em cima do próprio case do Newsfeed que já documentamos. Cada pergunta traz a questão feita pelo entrevistador e a resposta-modelo construída durante a prática, com o raciocínio explicado passo a passo.

## Pergunta 1 — Requisitos Funcionais vs. Não Funcionais

> *Pergunta do entrevistador:* **"Como você definiria a diferença entre um requisito funcional e um requisito não funcional, no contexto de um sistema como um newsfeed?"**

**Resposta modelo.**

A ideia central é que requisito funcional responde "o quê" o sistema faz; requisito não funcional responde "quão bem" ele faz isso. São duas perguntas diferentes sobre o mesmo sistema.

**Funcionais (as features que o usuário usa):** criar um post (texto, imagem ou vídeo), seguir/deixar de seguir outros usuários, visualizar o newsfeed (posts de quem você segue, em ordem cronológica reversa), curtir e comentar, e receber notificações quando alguém interage com seu post.

**Não funcionais (a qualidade/comportamento por trás dessas features, com métrica associada):** disponibilidade (o sistema no ar 99,999% do tempo), consistência eventual (tudo bem um post levar ~2 segundos para propagar), latência (o feed deve carregar em 1–2 segundos), escalabilidade (suportar 500 milhões de usuários ativos diários e 2 bilhões mensais), extensibilidade (fácil adicionar features novas, como responder a um comentário) e usabilidade (renderização rápida, sem texto aparecer sem a imagem).

"Eu separaria os requisitos em duas categorias. Os funcionais definem o que o sistema precisa fazer — nesse caso, criar posts, seguir usuários, ver o feed, curtir/comentar e notificar. Os não funcionais definem os atributos de qualidade que esse sistema precisa ter — coisas como disponibilidade, latência e escalabilidade — e eu gosto de colocar um número em cada um deles quando possível, tipo '99,999% de disponibilidade' ou 'feed carregando em até 2 segundos', porque isso me ajuda a guiar as decisões técnicas mais pra frente no design."

## Pergunta 2 — Classificando os Requisitos da Priya

> *Pergunta do entrevistador:* **"Uma desenvolvedora júnior, Priya, escreveu esta lista de requisitos e está em dúvida sobre como categorizá-los: (1) 'o sistema deve permitir que usuários façam upload de fotos e vídeos'; (2) 'o sistema deve estar disponível 99,999% do tempo'; (3) 'o sistema deve suportar 500 milhões de usuários ativos diários'; (4) 'o sistema deve permitir que usuários busquem outros usuários pelo nome'. Quais desses você classificaria como requisitos funcionais, e quais como não funcionais?"**

**Resposta modelo.**

**1) Upload de fotos e vídeos → Funcional.** É uma ação/capacidade que o usuário realiza — mesma categoria de "criar post".

**2) Disponibilidade de 99,999% → Não funcional.** É uma métrica de qualidade (disponibilidade), não uma feature.

**3) Suportar 500 milhões de DAU → Não funcional.** Essa é a pegadinha: parece uma regra de negócio, mas na verdade é escala/capacidade — não descreve uma ação que o usuário faz, descreve quão bem o sistema aguenta carga. É escalabilidade, mesma categoria de disponibilidade e latência.

**4) Buscar usuários por nome → Funcional.** De novo, é uma ação que o usuário executa (buscar), então é uma feature.

Heurística útil para verbalizar: "eu me pergunto se a frase descreve uma ação que o usuário consegue realizar no sistema (funcional) ou uma característica mensurável de quão bem o sistema se comporta — disponibilidade, escala, velocidade (não funcional)".

"Eu diria que os itens 1 e 4 são requisitos funcionais, e os itens 2 e 3 são não funcionais. Fazer upload de fotos e vídeos, e buscar outros usuários pelo nome, são ambos funcionais — descrevem ações que o usuário realmente executa no sistema. Estar disponível 99,999% do tempo e suportar 500 milhões de usuários ativos diários são ambos não funcionais. O terceiro é um pouco traiçoeiro porque parece uma regra de negócio, mas na verdade descreve escala — quanta carga o sistema precisa aguentar —, não uma feature com a qual o usuário interage. Por isso eu agruparia junto de disponibilidade, latência e outros atributos de qualidade, já que todos descrevem quão bem o sistema se comporta, e não o que ele faz."

## Pergunta 3 — Impacto da Escala e da Latência no Design

> *Pergunta do entrevistador:* **"Dado que precisamos suportar 500 milhões de usuários ativos diários com uma meta de latência abaixo de 2 segundos, como você acha que esse requisito não funcional específico influencia as escolhas arquiteturais do newsfeed, particularmente em como armazenamos e recuperamos os dados?"**

**Resposta modelo.**

**1) Identifique o desequilíbrio leitura/escrita.** Com 500M DAU, calculamos ~50 bilhões de leituras de feed por dia contra ~50 milhões de escritas (criação de post) por dia — um sistema extremamente read-heavy (leitura ~1000× mais frequente que escrita). Qualquer decisão de arquitetura deve otimizar para leitura rápida, mesmo que isso custe mais caro na escrita.

**2) Explique por que a abordagem ingênua falha.** Se, a cada leitura, o sistema tivesse que buscar quem você segue, buscar os posts de cada um e ordenar tudo na hora (join + sort em tempo de leitura), isso nunca cumpriria os 2 segundos de latência em escala de 50 bilhões de leituras/dia.

**3) Apresente a solução — fan-out na escrita.** Pré-computar o feed de cada usuário no momento da escrita, não da leitura. Quando alguém posta, o sistema distribui esse post para o feed de todos os seguidores (via message queue + news feed generator service), guardando isso pronto no FeedsDB e no Feeds Cache — assim, ler o feed vira uma busca direta em cache, não um cálculo complexo.

**4) Conecte com a escolha de banco de dados.** PostsDB, FeedsDB, CommentsDB e LikesDB são NoSQL porque a consulta é sempre simples (por post ID ou user ID), sem precisar de joins complexos, e NoSQL escala horizontalmente melhor nesse volume. FollowDB é GraphDB porque, no fan-out, o sistema precisa achar rapidamente "quem segue esse usuário" — uma consulta de grafo nativa.

**5) Mencione a mídia.** Como posts de imagem/vídeo dominam o volume de dados, as URLs de mídia ficam atrás de uma CDN — o dado pesado nunca precisa passar pelo banco de dados principal na hora da leitura, só a URL.

"Com 500 milhões de usuários ativos diários e uma meta de latência abaixo de 2 segundos, o maior sinal aqui é a proporção leitura/escrita — na estimativa de capacidade, encontramos cerca de 50 bilhões de leituras de feed por dia contra 50 milhões de escritas de post por dia. Então esse é um sistema extremamente read-heavy, e isso precisa guiar a arquitetura. Se calculássemos o feed na hora, para cada leitura, buscando quem você segue, os posts de cada um e ordenando tudo, isso simplesmente não escalaria para 50 bilhões de leituras por dia dentro de um orçamento de 2 segundos. Em vez disso, movemos esse trabalho para o momento da escrita: quando um usuário cria um post, distribuímos isso via fila de mensagens para um news feed generator service, que grava o post diretamente no feed pré-computado de cada seguidor, guardado num FeedsDB e num feeds cache. Assim, ler o feed vira uma simples busca em cache, em vez de um join e ordenação caros. Esse perfil read-heavy também explica nossas escolhas de banco de dados: PostsDB, FeedsDB, CommentsDB e LikesDB são todos NoSQL, porque o padrão de acesso é sempre uma busca simples por post ID ou user ID, e NoSQL escala horizontalmente muito mais fácil nesse volume do que um banco relacional. O FollowDB é um banco de grafo especificamente porque, durante o fan-out, precisamos encontrar eficientemente todos os seguidores de um usuário — o que é uma consulta de grafo nativa. E, como mídia domina o volume de dados, servimos imagens e vídeos através de uma CDN, para que o payload pesado nunca precise ser buscado da origem a cada leitura."

## Pergunta 4 — Requisito de Segurança: Encriptação em Repouso e em Trânsito

> *Pergunta do entrevistador:* **"Priya adicionou um novo requisito ao documento: 'o sistema deve garantir que todos os dados do usuário sejam encriptados em repouso e em trânsito, para manter a privacidade do usuário'. Como você categorizaria esse requisito, e por que é importante incluí-lo nas especificações?"**

**Resposta modelo.**

É um requisito não funcional — mais especificamente, um requisito de segurança (às vezes tratado como sub-categoria própria, junto de privacidade/compliance). Não é uma ação que o usuário realiza (isso seria funcional); é uma característica de como o sistema protege os dados por trás de todas as ações — no mesmo grupo de disponibilidade, latência e escalabilidade, só que sobre confidencialidade em vez de performance.

**1) Motivo legal/de compliance.** Sistemas que armazenam dados pessoais (posts, fotos, relacionamentos entre usuários) geralmente precisam seguir regulações como a LGPD (aqui no Brasil) ou o GDPR (Europa), que exigem proteção adequada de dados pessoais — deixar isso de fora da spec é um risco jurídico real, não só técnico.

**2) Motivo de confiança/reputação.** Um vazamento de dados de usuários (fotos privadas, mensagens, relacionamentos de quem segue quem) é o tipo de incidente que destrói a confiança dos usuários na plataforma.

**3) Impacto concreto na arquitetura.** "Encriptado em trânsito" significa TLS/HTTPS entre o cliente e o API gateway, e idealmente entre os serviços internos e os bancos de dados também; "encriptado em repouso" significa habilitar encryption at rest nos bancos (PostsDB, FeedsDB, CommentsDB, LikesDB, FollowDB) e no object storage (o S3 já oferece isso nativamente, inclusive nas pre-signed URLs vistas no deep dive).

**4) Torne o requisito mensurável.** Mesmo sendo um requisito qualitativo, vale deixá-lo verificável — por exemplo, "AES-256 em repouso" e "TLS 1.2 ou superior em trânsito" — em vez de deixar vago como "deve ser seguro".

"Eu categorizaria isso como um requisito não funcional, especificamente um requisito de segurança. Não descreve uma ação que o usuário realiza — descreve uma qualidade que o sistema precisa garantir em todas as ações, parecido com disponibilidade ou latência, mas para confidencialidade dos dados em vez de performance. É importante incluir por alguns motivos: primeiro, há um ângulo legal e de compliance — sistemas que lidam com dados pessoais, como posts, fotos e relacionamentos de seguidores, tipicamente precisam seguir regulações como a LGPD ou o GDPR, que exigem proteção adequada de dados pessoais; deixar isso de fora da spec não é só uma lacuna técnica, é um risco legal. Segundo, há um ângulo de confiança — um vazamento de dados envolvendo fotos privadas ou relacionamentos é exatamente o tipo de incidente que destrói a confiança do usuário na plataforma. Na prática, esse requisito afetaria nossa arquitetura de duas formas: encriptação em trânsito significa TLS entre o cliente e o API gateway, e idealmente entre os serviços internos e os bancos de dados também; encriptação em repouso significa habilitar encriptação no PostsDB, FeedsDB, CommentsDB, LikesDB e FollowDB, além do object storage — que, se estivermos usando algo como o Amazon S3, já suporta encriptação server-side nativamente, inclusive nos uploads via pre-signed URL que vimos no deep dive. E eu sugeriria à Priya um refinamento: mesmo sendo um requisito qualitativo, deveríamos torná-lo mensurável quando possível — especificando, por exemplo, AES-256 em repouso e TLS 1.2 ou superior em trânsito — em vez de deixá-lo vago como 'deve ser seguro'."
