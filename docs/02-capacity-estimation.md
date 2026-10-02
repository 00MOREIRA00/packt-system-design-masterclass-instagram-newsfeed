<!-- Parte do case: Sistema de Newsfeed (Instagram) — System Design Masterclass -->

# Estimativa de Capacidade

Vamos para a segunda parte do system design, que é a estimativa de capacidade. Vamos ver cinco pontos diferentes. O primeiro é definir os usuários ativos diários (DAU) e os usuários ativos mensais (MAU). O segundo é o throughput — essa é a quantidade de requisições que o nosso sistema processa por unidade de tempo (por segundo ou, como vamos usar aqui, por dia). O terceiro é armazenamento (storage), o quarto é memória (memory), e o último é rede (network). Bem, eu sei que estou jogando muitas palavras aqui — não precisa se preocupar, vamos ver cada um deles em profundidade nas próximas partes.

## Usuários Ativos Diários e Mensais (DAU/MAU)

Vamos ver a primeira parte da nossa estimativa de capacidade, que são os usuários ativos diários e os usuários ativos mensais. Aqui, vamos definir quantos usuários usam o nosso sistema diariamente.

Então vamos definir os usuários ativos diários (DAU). Estamos assumindo que os usuários ativos diários do nosso sistema são algo em torno de 500 milhões. Então vamos só anotar esse número.

A segunda coisa que vamos definir aqui são os usuários ativos mensais (MAU). Vamos assumir que existem 2 bilhões de usuários ativos mensais para o nosso sistema de newsfeed.

## Throughput (Vazão)

Agora vamos para a segunda parte da nossa estimativa de capacidade, que é o throughput. Aqui vamos definir duas partes: o throughput de escrita (write throughput) e o throughput de leitura (read throughput). Vamos olhar cada um deles mais a fundo.

Se estamos tentando definir o throughput correto para o nosso sistema, precisamos decidir como escrevemos no sistema. De acordo com os nossos requisitos funcionais, existem três formas possíveis de escrita:

**Criar posts.** Os usuários criam posts, e isso vai para o nosso sistema — ou seja, os usuários estão escrevendo no sistema.

**Seguir.** Os usuários seguem uns aos outros. O sistema precisa armazenar quem segue quem — é isso que o sistema escreve quando um usuário segue outro.

**Comentar ou curtir.** Quando um usuário curte ou comenta no post de outra pessoa, esse dado (quem curtiu ou comentou em qual post) vai para o sistema e é salvo lá — ou seja, também é escrita.

Entre esses três tipos de escrita, vamos olhar mais a fundo para a criação de posts, porque é o mais pesado: um post normalmente consiste em texto, imagem ou vídeo, sendo mais volumoso que os outros. Por isso, vamos calcular o throughput de escrita com base na criação de posts.

Vamos assumir que 10% dos usuários ativos diários postam em um dia (na prática, ninguém posta todo dia). Isso é 10% de 500 milhões de usuários ativos diários, o que dá 50 milhões de usuários. Ou seja, haveria 50 milhões de requisições de criação de post por dia — esse é o nosso throughput de escrita: 50 milhões de requisições por dia.

Agora vamos ao throughput de leitura. Pelos requisitos funcionais, existe principalmente uma forma de o usuário ler dados do sistema: quando ele visualiza o seu newsfeed. Para estimar isso, vamos assumir que um usuário normal abre o newsfeed 10 vezes por dia, e cada vez que abre, vê 10 posts — ou seja, um usuário lê 100 posts por dia.

Se temos 500 milhões de usuários ativos diários, e cada um deles abre 100 posts por dia, isso dá 500 milhões × 100, que é igual a 50 bilhões de requisições de leitura por dia. Sinceramente, isso é bastante coisa — é impressionante que sistemas como Instagram ou Twitter conseguem lidar com 50 bilhões de requisições de leitura por dia.

Isso conclui a nossa parte de throughput, em que calculamos o throughput de leitura e de escrita para o nosso sistema de newsfeed.

## Estimativa de Armazenamento (Storage)

A terceira parte da estimativa de capacidade é a estimativa de armazenamento (storage). A primeira pergunta que precisamos nos fazer é: o que estamos tentando armazenar? Estamos tentando armazenar os posts. Se multiplicarmos o tamanho de um post pelo número de posts em um dia, isso nos dá o armazenamento total.

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

Esse é o armazenamento total em um dia. Mas e em 10 anos? Basta multiplicar os 216 TB por dia por 365 × 10, o que dá 788.400 TB — algo em torno de 790 petabytes.

## Memória (Cache)

Vamos para a próxima parte da estimativa de capacidade, que é a memória. Por memória, queremos dizer o tamanho da memória de cache (cache memory). Todos sabemos que acessar dados do banco de dados demora bastante tempo. Mas, se quisermos acessar esses dados mais rápido, usamos caches.

A quantidade de memória de cache necessária por dia pode ser simplesmente considerada como 1% do armazenamento diário, o que é igual a 0,01 × 216 TB, resultando em algo em torno de 2 TB por dia.

## Rede / Bandwidth (Ingress e Egress)

Agora vamos para a estimativa de rede (network) ou largura de banda (bandwidth). Essa estimativa é essencial porque nos diz quanto dado flui para dentro e para fora do nosso sistema por segundo. O dado que entra no sistema é chamado de ingress (entrada), e o dado que sai do sistema é chamado de egress (saída).

**Ingress.** O dado que entra em um dia acaba sendo salvo no armazenamento. Já sabemos, pelo cálculo de armazenamento, que os dados armazenados em um dia equivalem a 216 TB — portanto, o dado que entra em um dia também é 216 TB. Mas, para calcular o ingress, precisamos do valor por segundo, não por dia: dividimos 216 TB por 24 × 60 × 60, o que dá 2,5 GB por segundo.

**Egress.** É quanto dado sai do sistema por segundo — basicamente, todo o dado que está sendo lido. Pela estimativa de throughput, sabemos que existem 50 bilhões de requisições de leitura por dia. Multiplicando esse número pelo tamanho médio de um post, obtemos quanto dado sai do sistema em um dia.

O tamanho médio do post é: 0,2 × 100 KB + 0,6 × 0,5 MB + 0,2 × 20 MB = 4,3 MB (onde 0,2 / 0,6 / 0,2 são as porcentagens de posts de texto, imagem e vídeo, e 100 KB / 0,5 MB / 20 MB são os respectivos tamanhos médios).

Multiplicando as 50 bilhões de requisições de leitura por dia por 4,3 MB, obtemos 216 petabytes por dia de dado saindo do sistema. Dividindo esse número por 24 × 60 × 60, chegamos ao egress: 2,5 TB por segundo.

| Direção | Por dia | Por segundo |
| --- | --- | --- |
| Ingress (entrada) | 216 TB | **2,5 GB/s** |
| Egress (saída) | 216 PB | **2,5 TB/s** |

Com isso, fechamos os cinco pontos da estimativa de capacidade do nosso sistema de newsfeed: DAU/MAU, throughput, armazenamento, memória (cache) e rede/bandwidth.

> **Resumo rápido — pontos-chave para a entrevista**
>
> - DAU/MAU: 500 milhões de usuários ativos diários, 2 bilhões mensais.
> - Throughput: ~50 milhões de requisições de escrita/dia (criação de post) e ~50 bilhões de requisições de leitura/dia (abrir o feed).
> - Storage: 216 TB/dia (~790 PB em 10 anos) — vídeo domina o total mesmo sendo só 20% dos posts, por ser o formato mais pesado.
> - Memória (cache): ~2 TB/dia (1% do armazenamento diário).
> - Rede: ingress 2,5 GB/s, egress 2,5 TB/s — egress é ~1000× maior porque cada post é lido muito mais vezes do que é escrito.
> - Dica de entrevista: identifique sempre o gargalo (aqui, egress/leitura) — é ele que vai guiar decisões de cache e CDN no high-level design.
