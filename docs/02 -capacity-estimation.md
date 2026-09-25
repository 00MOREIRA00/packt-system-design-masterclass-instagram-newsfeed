# Estimativa de Capacidade 

Vamos para a segunda parte do system designe, que é a estimativa de capacidade. Vamos ver cinco pontos diferentes. O primeiro é definir os usuários ativos diários (DAU) e os usuários ativos mensais (MAU). O segundo é throughput (storage), o quarto é memoria (memory), e o ultimo é rede (network).

## Usuários Ativos Diários e Mensáis (DAU/MAU)

Vamos primeiramente trabalhar em cima da estimativa de capacidade, para os usuários ativos mensais e usuários ativos diarios. O que isso quer dizer ? Aqui vamos definir quantos usuários usam o nosso sistema diarimente.

### DAU (Usuário Ativos Diários)

Vamos assumir que tenhamos algo em torno de 500 milhões de usuários para nosso sistema durante o dia 

### MAU (Usuário Ativos Mensais)

Vamos assumir que tenhamos algo em torno de 2 bilhões de usuário para nosso sistema durante o mês

## Throughput 

Vamos defiinir dois tipos possiveis: Throughput de escrita e de leitura. Para que possamos definir uma estimativa condizente com o que esperamos dos requisitos do sistema.

### Throughput de Escrita 

Temos como requisitos para aplicação `criar post`, `seguir` e `comentar e curtir`. Para esse caso vamos calcular somente em cima da criação de post.

Vamos assumir que 10% dos usuários ativos diários postam em um dia (na prática, ninguém posta todo dia). Isso é 10% de 500 milhões de usuários ativos diários, o que dá 50 milhões de usuários. Ou seja, haveria 50 milhões de requisições de criação de post por dia — esse é o nosso throughput de escrita: 50 milhões de requisições por dia.

### Throughput de Leitura

Analisando temos a leitura de ffed como a principal forma de requisito quando falamos de leitura, então faremos o calculo em cima disso, e cada vez que abre, vê 10 posts — ou seja, um usuário lê 100 posts por dia.

Se temos 500 milhões de usuários ativos diários, e cada um deles abre 100 posts por dia, isso dá 500 milhões × 100, que é igual a 50 bilhões de requisições de leitura por dia. Sinceramente, isso é bastante coisa — é impressionante que sistemas como Instagram ou Twitter conseguem lidar com 50 bilhões de requisições de leitura por dia.

### Estimativa de Armazenamento (Storage)

Analisando o que armazenaremos, temos em foco os posts. Se multiplicarmos o tamanho de um post pelo número de posts em um dia, isso nos dá o armazenamento total.

No nosso caso, existem três tipos de posts: post de texto, post de imagem e post de vídeo. Vamos fazer dois tipos de suposições (assumptions).

**Tamanho médio do post:** os posts de texto costumam ter 100 KB, os posts de imagem 0,5 MB, e os posts de vídeo 20 MB.

**Porcentagem de cada tipo de post:** 20% dos posts são de texto, 60% são de imagem e 20% são de vídeo.

Com base nessas duas suposições e no throughput de 50 milhões de requisições de criação de post por dia, podemos calcular o armazenamento:

| Tipo de post | % dos posts | Tamanho médio | Cálculo → armazenamento/dia |
| --- | --- | --- | --- |
| Texto | 20% | 100 KB | 0,2 × 50.000.000 × 100 KB = 1 TB/dia |
| Imagem | 60% | 0,5 MB | 0,6 × 50.000.000 × 0,5 MB = 15 TB/dia |
| Vídeo | 20% | 20 MB | 0,2 × 50.000.000 × 20 MB = 200 TB/dia |
| **Total** | 100% | — | **216 TB/dia** |

Esse é o armazenamento total em um dia. Mas e em 10 anos? Basta multiplicar os 216 TB por dia por 365 × 10, o que é igual a 750 petabytes.

## Memória (Cache)

Para evitar a demora de acessar o banco de dados, usamos o cache. A quantidade de armazenamento diário a 0,01 x 216 TB, resultando em algo em torno de 2 TB por dia.

## Rede / Bandwidth (Ingress e Egress)

Aqui vamos ver a estimativa de rede (network) ou largura de banda (bandwidth). Fazemos essa estimativa para termos nossão de quanto de dado flui para dentro e fora do nosso sistema por segundo. 

Os dados de entrada chamadas de ingress, e os dados de saída chamadas de egress.

### Ingress

O dado de entrada em um dia acaba sendo armazenado. Para sabermos o valor do igress precisamos do valor por segundoo, logo pegamos o valor total do dia 216 TB e deividimos por 24 * 60 * 60, o que da 2,5 GB por segundo.

### Egress 

O dado de saída do sistema por segundo - basicamente , todo dado que está sendo lido. Pela estimatiiva de throughput, sabemos que existem 50 bilhões de requisições de leitura por dia. Multiplicando esse número pelo tamanho médio de um post, obtemos quanto dado sai do sistema em um dia.

Multiplicando as 50 bilhões de requisições de leitura por dia por 4,3 MB, obtemos 216 petabytes por dia de dado saindo do sistema. Dividindo esse número por 24 × 60 × 60, chegamos ao egress: 2,5 TB por segundo.

| Direção | Por dia | Por segundo |
| --- | --- | --- |
| Ingress (entrada) | 216 TB | **2,5 GB/s** |
| Egress (saída) | 216 PB | **2,5 TB/s** |

Com isso, fechamos os cinco pontos da estimativa de capacidade do nosso sistema de newsfeed: DAU/MAU, throughput, armazenamento, memória (cache) e rede/bandwidth.

---

> **Resumo rápido — pontos-chave para a entrevista**
>
> - DAU/MAU: 500 milhões de usuários ativos diários, 2 bilhões mensais.
> - Throughput: ~50 milhões de requisições de escrita/dia (criação de post) e ~50 bilhões de requisições de leitura/dia (abrir o feed).
> - Storage: 216 TB/dia (750 PB em 10 anos) — vídeo domina o total mesmo sendo só 20% dos posts, por ser o formato mais pesado.
> - Memória (cache): ~2 TB/dia (1% do armazenamento diário).
> - Rede: ingress 2,5 GB/s, egress 2,5 TB/s — egress é ~1000× maior porque cada post é lido muito mais vezes do que é escrito.
> - Dica de entrevista: identifique sempre o gargalo (aqui, egress/leitura) — é ele que vai guiar decisões de cache e CDN no high-level design.