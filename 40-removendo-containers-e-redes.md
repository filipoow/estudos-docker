# Removendo containers e redes: mantendo os recursos organizados

## Fechando o ciclo: o que se cria precisa ser limpo

Containers, volumes e redes seguem a mesma lógica de ciclo de vida: criar, usar, e remover quando não for mais necessário. Os arquivos sobre [removendo containers](15-removendo-containers.md) e [removendo volumes inativos](31-removendo-volumes-inativos.md) já cobriram dois desses recursos. Este fecha o conjunto com redes.

## Removendo containers primeiro

Redes só podem ser removidas quando nenhum container está conectado a elas, então a ordem importa: primeiro os containers, depois a rede. Os comandos são os já apresentados:

```
docker stop servidor cliente
docker rm servidor cliente
```

Ou, para forçar a remoção de containers ainda em execução, como visto no arquivo sobre [removendo containers](15-removendo-containers.md):

```
docker rm -f servidor cliente
```

## Removendo uma rede: docker network rm

```
docker network rm teste-rede
```

Remove uma rede específica, desde que nenhum container esteja mais conectado a ela. As três redes criadas automaticamente pelo Docker, `bridge`, `host` e `none`, não podem ser removidas, já que fazem parte do funcionamento básico do próprio Docker.

## Quando a rede não quer ser removida

Um erro comum ao tentar remover uma rede é a mensagem informando que ela tem endpoints ativos. Isso significa que ainda há pelo menos um container conectado, mesmo que parado. A opção `-f` do `docker network rm` não resolve isso, ao contrário do que o nome sugere: ela só evita o erro caso a rede não exista. A solução é desconectar os containers antes, com o `docker network disconnect` apresentado no arquivo sobre [criação e gerenciamento de redes](36-docker-network-create-inspect-connect.md), ou remover os próprios containers.

```
docker network disconnect teste-rede cliente
docker network rm teste-rede
```

## Limpando redes não usadas em massa: docker network prune

```
docker network prune
```

Remove todas as redes que não têm nenhum container conectado, pedindo confirmação antes. Assim como os comandos de limpeza equivalentes para containers e volumes, ele aceita a opção `--force`, para pular a confirmação, e filtros de tempo, como `--filter "until=24h"`, para remover só redes mais antigas que determinado prazo. É um comando relativamente seguro de usar, já que redes, ao contrário de volumes, não guardam dados, e recriar uma rede removida por engano é simples e rápido.

## Um conjunto completo de limpeza

Juntando tudo o que foi visto nos arquivos sobre limpeza, existe ainda um comando mais amplo, `docker system prune`, que remove de uma vez containers parados, redes não usadas, imagens órfãs e cache de build. Como já apresentado no arquivo sobre [removendo volumes inativos](31-removendo-volumes-inativos.md), ele não remove volumes por padrão, justamente para evitar perda acidental de dados.

## Fontes

- [Docker Network Management, CodeSignal](https://codesignal.com/learn/courses/networking-and-cross-container-communication/lessons/docker-network-management)
- [How to Fix Docker "Network Has Active Endpoints" Errors, oneuptime](https://oneuptime.com/blog/post/2026-02-08-how-to-fix-docker-network-has-active-endpoints-errors/view)
- [How to Remove Docker Images, Containers, Volumes, and Networks, Linuxize](https://linuxize.com/post/how-to-remove-docker-images-containers-volumes-and-networks/)
