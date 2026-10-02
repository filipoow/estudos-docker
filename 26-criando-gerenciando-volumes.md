# Criando e gerenciando volumes: docker volume create, ls e inspect

## Um conjunto próprio de comandos

Assim como existem comandos específicos para gerenciar containers, apresentados no arquivo sobre [comandos básicos do Docker CLI](09-docker-cli-comandos-basicos.md), existe um grupo de comandos dedicado a volumes, todos começando com `docker volume`. Este arquivo apresenta os principais.

## Criando um volume: docker volume create

```
docker volume create meus-dados
```

Esse comando cria um volume chamado `meus-dados`, vazio, guardado e gerenciado pelo Docker. O volume passa a existir de forma independente de qualquer container, ainda sem nenhum conectado a ele, esperando para ser usado.

Vale notar que criar o volume antes não é obrigatório: se um container for iniciado referenciando um volume que ainda não existe, o Docker o cria automaticamente na hora. Mesmo assim, criar de forma explícita costuma ser considerado uma boa prática, especialmente em contextos onde o volume precisa de opções específicas, ou onde a organização do ambiente importa.

## Listando volumes: docker volume ls

```
docker volume ls
```

Mostra todos os volumes existentes na máquina, com o driver usado por cada um e o nome. Funciona de forma análoga ao `docker ps` para containers e ao `docker images` para imagens, já apresentados no arquivo sobre [comandos básicos](09-docker-cli-comandos-basicos.md). É o ponto de partida para qualquer inventário de quais volumes existem, incluindo os que talvez nem estejam mais em uso.

## Inspecionando um volume: docker volume inspect

```
docker volume inspect meus-dados
```

Devolve, em formato JSON, os detalhes completos de um volume específico: a data de criação, o driver, o escopo, e, principalmente, o `Mountpoint`, o caminho real, na máquina hospedeira, onde os dados daquele volume de fato estão guardados. Essa informação é especialmente útil para entender onde os dados realmente estão, e para operações como backup, apresentado no arquivo sobre [backup e restauração](29-backup-restauracao-volumes.md).

## Removendo um volume: docker volume rm

```
docker volume rm meus-dados
```

Remove um volume específico. O Docker recusa a remoção se algum container, mesmo parado, ainda estiver conectado àquele volume, uma proteção contra apagar dados que alguém ainda pode precisar. Remover vários volumes inativos de uma vez é um tema aprofundado no arquivo sobre [removendo volumes inativos](31-removendo-volumes-inativos.md).

## Por que gerenciar volumes de forma explícita

Volumes são, por natureza, a parte do ambiente Docker onde dados importantes realmente vivem. Diferente de containers e imagens, que podem ser descartados e recriados sem prejuízo, um volume apagado por engano é perda de dados real. Por isso vale o hábito de listar e inspecionar antes de remover qualquer coisa, da mesma forma que o cuidado com operações destrutivas em lote já foi destacado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/09-funcoes-arrays-e-tratamento-de-erros/05-find-exec-xargs-manipulacao-arquivos.md).

## Fontes

- [Docker Volume Commands, GeeksforGeeks](https://www.geeksforgeeks.org/devops/docker-volume-commands/)
- [Understanding Docker Volumes, Earthly Blog](https://earthly.dev/blog/docker-volumes/)
- [Docker Volumes: How to Create & Get Started, phoenixNAP](https://phoenixnap.com/kb/docker-volumes)
