# Requisitos Funcionais e Não Funcionais

Ao projetar um sistema, começamos definindo o que ele precisa fazer e quais qualidades ele precisa ter. Essas duas perguntas dão origem aos requisitos funcionais e aos não funcionais.

## Requisitos Funcionais

Os requisitos funcionais descrevem **ações que o sistema permite realizar**. No nosso sistema de newsfeed, temos principalmente cinco:

**1. Criar posts de rede social.** Os usuários têm o poder de criar um post de rede social. O post pode ser um post de texto, de imagem ou de vídeo.

**2. Seguir e deixar de seguir outros usuários.** Os usuários têm a capacidade de seguir ou deixar de seguir outro usuário.

**3. Newsfeed (feed de notícias).** Os usuários podem visualizar seu newsfeed. O feed mostra os posts dos usuários que eles seguem, em ordem cronológica reversa — do mais novo para o mais antigo.

**4. Curtir e comentar.** Os usuários podem curtir ou comentar nos posts de outras pessoas.

**5. Notificação do usuário.** Os usuários são notificados quando outro usuário curte ou comenta em seu post.

## Requisitos Não Funcionais

Os requisitos não funcionais descrevem **as qualidades e os limites que o sistema deve cumprir enquanto executa as funcionalidades**. Eles respondem a perguntas como: o feed precisa carregar em quanto tempo? Por quanto tempo o serviço pode ficar fora do ar? Quantas pessoas devem conseguir usá-lo ao mesmo tempo?

Pense no requisito funcional **“o usuário pode abrir o feed”**. Só isso não diz se o feed abre em um segundo ou em um minuto, nem se ele funciona quando milhões de pessoas acessam o sistema. Essas expectativas de velocidade, disponibilidade e capacidade são requisitos não funcionais. Eles definem **o resultado esperado**, sem exigir uma tecnologia ou arquitetura específica para alcançá-lo.

Uma boa forma de escrevê-los é indicar **o que será medido e qual resultado é aceitável**. Os números abaixo são hipóteses para este exercício de system design; em um projeto real, seriam negociados com base no público, no custo e nas necessidades do produto.

**1. Disponibilidade (availability).** É a proporção do tempo em que o serviço pode ser usado. Uma meta de **99,999% de disponibilidade** significa aceitar cerca de **5 minutos de indisponibilidade por ano**. Essa é uma meta exigente: precisa ser justificada pelo produto, pois influencia o custo e a arquitetura.

**2. Consistência eventual (eventual consistency).** Uma publicação pode ser aceita antes de aparecer no feed de todos os seguidores. Neste exercício, aceitamos um atraso de **até cerca de 2 segundos** para essa atualização. Isso não significa que o post possa desaparecer ou que as regras de privacidade possam ser ignoradas; significa apenas que diferentes leituras podem ver a atualização em momentos ligeiramente diferentes.

**3. Latência (latency).** É o tempo entre uma ação e a resposta percebida pelo usuário. Ao abrir a página inicial, queremos que o feed **carregue em até 1 a 2 segundos**. Para transformar essa intenção em uma meta de engenharia, ainda precisaríamos definir o que conta como “carregado” e em que porcentagem dos acessos esse tempo deve ser cumprido.

**4. Escalabilidade (scalability).** É a capacidade de manter o serviço funcionando bem quando o uso cresce. Podemos assumir, como cenário de dimensionamento, **500 milhões de usuários ativos por dia e 2 bilhões por mês**. Esses números, sozinhos, não determinam a infraestrutura: também precisamos estimar quantas vezes cada pessoa abre o feed, quantos posts cria e qual é o pico de acessos simultâneos.

**5. Extensibilidade (extensibility).** É a facilidade de acrescentar ou alterar funcionalidades sem reescrever grandes partes do sistema. Por exemplo, o projeto deve permitir incluir respostas a comentários ou recomendações de posts sem exigir uma reformulação completa do feed. É uma qualidade mais difícil de medir diretamente; podemos avaliá-la pelo esforço e pelas partes afetadas quando uma dessas mudanças for implementada.

**6. Usabilidade (usability).** É a facilidade de usar o produto e entender o que está acontecendo. Por exemplo, se a imagem de um post ainda estiver carregando, a interface deve mostrar esse estado com clareza e continuar utilizável. Isso é diferente da latência: mesmo uma resposta rápida pode oferecer uma experiência confusa.

> **Em uma frase:** requisitos funcionais dizem **o que o usuário consegue fazer**; requisitos não funcionais dizem **quão bem o sistema precisa funcionar ao permitir essas ações**.

---

> **Resumo rápido — pontos-chave para a entrevista**
>
> - Funcionais: criar post (texto/imagem/vídeo), seguir/deixar de seguir, ver o newsfeed (ordem cronológica reversa), curtir/comentar, notificar o dono do post.
> - Não funcionais: alta disponibilidade (99,999%), consistência eventual (~2s de atraso é aceitável), baixa latência (feed carrega em 1–2s), alta escalabilidade (500M DAU / 2B MAU), extensibilidade e boa usabilidade (renderização rápida de mídia).
> - Dica de entrevista: sempre alinhe com o entrevistador o número de usuários e o nível de consistência assumidos — são suposições, não fatos, e o entrevistador quer ver você negociando o escopo.