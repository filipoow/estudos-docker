# Removendo containers parados e forçando a remoção dos ativos

## Parar não é o mesmo que apagar

O arquivo sobre o [ciclo de vida de um container](10-ciclo-de-vida-container.md) explicou que um container no estado Exited ainda existe no disco, ocupando espaço e mantendo seus logs e sistema de arquivos, mesmo sem consumir processador ou memória. Remover esse container por completo, apagando-o de vez, é uma etapa separada, feita pelo `docker rm`.

## Removendo um container parado

```
docker rm meu-app
```

Esse comando exige que o container já esteja parado. Se ele ainda estiver rodando, o Docker recusa a remoção, como uma proteção básica contra apagar algo que ainda está ativamente em uso.

## Removendo vários containers de uma vez

Um padrão comum é combinar `docker rm` com filtros do próprio `docker ps`, apresentado no arquivo sobre [comandos básicos](09-docker-cli-comandos-basicos.md), para remover em massa todos os containers já parados:

```
docker rm $(docker ps --filter status=exited -q)
```

Esse comando busca os IDs (`-q`, de "quiet", que mostra só os identificadores) de todos os containers com status `exited`, e passa esses IDs como argumento para o `docker rm`, removendo todos de uma vez, um padrão parecido com o uso de `xargs` já apresentado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/09-funcoes-arrays-e-tratamento-de-erros/05-find-exec-xargs-manipulacao-arquivos.md).

## Forçando a remoção de um container em execução

```
docker rm -f meu-app
```

A opção `-f` (ou `--force`) contorna a proteção padrão: ela envia um sinal de encerramento forçado (SIGKILL) ao container em execução, e o remove imediatamente, sem dar chance de um encerramento organizado. É um comando útil em situações de emergência, ou em scripts de limpeza automatizada, mas deve ser usado com atenção, exatamente pela mesma razão que operações destrutivas em lote merecem cuidado extra, já discutida no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/09-funcoes-arrays-e-tratamento-de-erros/05-find-exec-xargs-manipulacao-arquivos.md): um filtro errado pode remover, à força, containers que ainda importavam.

## Uma alternativa mais segura para limpeza em massa: docker container prune

```
docker container prune
```

Esse comando remove todos os containers parados de uma vez, mas com uma diferença importante em relação ao exemplo anterior com `docker rm`: ele pede confirmação antes de agir, mostrando quanto espaço seria liberado. Para pular essa confirmação, existe a opção `-f`:

```
docker container prune -f
```

É possível também filtrar quais containers parados devem ser removidos, por exemplo só os parados há mais de um dia:

```
docker container prune --filter "until=24h"
```

## Fontes

- [docker container rm, Docker Docs](https://docs.docker.com/reference/cli/docker/container/rm/)
- [Prune unused Docker objects, Docker Docs](https://docs.docker.com/engine/manage-resources/pruning/)
- [How to Remove Docker Images, Containers, Volumes, and Networks, Linuxize](https://linuxize.com/post/how-to-remove-docker-images-containers-volumes-and-networks/)
