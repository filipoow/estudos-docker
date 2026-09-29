# A diferença entre docker create e docker run

## Dois comandos que parecem fazer a mesma coisa

O arquivo sobre [comandos básicos do Docker CLI](09-docker-cli-comandos-basicos.md) apresentou `docker run` como o comando padrão para colocar um container no ar. Existe também o `docker create`, menos usado no dia a dia, mas que revela algo importante sobre o que realmente acontece por baixo dos panos quando um container nasce.

## `docker create`: só a preparação

```
docker create --name meu-app nginx
```

O `docker create` monta a estrutura completa de um novo container a partir de uma imagem, incluindo a camada de escrita própria dele, deixando-o pronto para rodar, mas sem de fato iniciar o processo principal. O container criado dessa forma fica no estado Created, já apresentado no arquivo sobre o [ciclo de vida de um container](10-ciclo-de-vida-container.md), até que alguém explicitamente o inicie com `docker start`.

```
docker start meu-app
```

## `docker run`: preparação e execução, de uma vez

```
docker run --name meu-app nginx
```

O `docker run` faz tudo que o `docker create` faz, e na sequência já executa o `docker start` automaticamente. Em termos práticos, `docker run` é equivalente a rodar `docker pull` (se a imagem ainda não existir localmente), seguido de `docker create`, seguido de `docker start`, tudo isso condensado num único comando.

## Por que, então, usar docker create separadamente

Se `docker run` já faz tudo, a existência do `docker create` sozinho pode parecer desnecessária à primeira vista. Ele se torna útil justamente quando existe um motivo para separar a criação da execução: preparar vários containers de antemão, por exemplo, e só iniciá-los todos juntos depois, num momento específico, ou inspecionar e ajustar a configuração de um container (como redes ou volumes conectados a ele) antes de deixá-lo efetivamente rodar.

Na prática do dia a dia, porém, a esmagadora maioria dos casos de uso não precisa dessa separação, e por isso `docker run` é, disparado, o comando mais usado, cobrindo o fluxo mais comum: criar e já rodar, tudo de uma vez.

## Gerenciando o ciclo de vida completo

Entender essa diferença ajuda a enxergar o ciclo de vida completo de um container como uma sequência de comandos específicos, cada um responsável por uma transição de estado: `docker create` leva ao estado Created, `docker start` leva ao estado Running, `docker stop` leva ao estado Exited, e `docker rm`, apresentado no arquivo sobre [removendo containers](15-removendo-containers.md), remove o container por completo, encerrando esse ciclo de vida de uma vez por todas.

## Fontes

- [Docker Run vs Start vs Create: Difference Explained, Linux Handbook](https://linuxhandbook.com/docker-run-vs-start-vs-create/)
- [Docker Tip #61: Difference between Docker Create, Start and Run, Nick Janetakis](https://nickjanetakis.com/blog/docker-tip-61-difference-betweeen-docker-create-start-and-run)
- [Docker Start vs Docker Run, GeeksforGeeks](https://www.geeksforgeeks.org/devops/docker-start-vs-docker-run/)
