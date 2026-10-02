# Estratégias de remoção de volumes inativos

## Volumes se acumulam em silêncio

Diferente de containers parados, que aparecem em `docker ps -a` e costumam chamar atenção, volumes esquecidos passam despercebidos com facilidade. Cada container criado de forma descuidada pode deixar um volume para trás, e, com o tempo, esses volumes órfãos acumulam gigabytes de dados que ninguém mais usa, ocupando espaço em disco sem que ninguém perceba, até o dia em que o disco enche. Vale ter uma estratégia consciente para identificá-los e removê-los.

## Primeiro, identificar o que está solto

Antes de remover qualquer coisa, o passo seguro é listar os volumes que não estão conectados a nenhum container, usando o filtro `dangling`:

```
docker volume ls -f dangling=true
```

Esse comando, uma variação do `docker volume ls` apresentado no arquivo sobre [criação e gerenciamento de volumes](26-criando-gerenciando-volumes.md), mostra apenas os volumes sem nenhum container associado, o equivalente de olhar a lista de "candidatos à remoção" antes de apagar.

## Removendo um volume específico

```
docker volume rm nome-do-volume
```

Remove um volume individual, depois de conferido que ele realmente não é mais necessário. O Docker recusa a remoção se algum container ainda estiver conectado a ele, mesmo parado, como proteção.

## Removendo todos os volumes não usados: docker volume prune

```
docker volume prune
```

Remove em massa os volumes que não estão sendo usados por nenhum container, pedindo confirmação antes de agir. É a versão para volumes do `docker container prune`, apresentado no arquivo sobre [removendo containers](15-removendo-containers.md). Por padrão, esse comando remove só volumes anônimos (os criados automaticamente, sem um nome dado pelo usuário). Para incluir também os volumes nomeados não usados, existe a opção `--all`.

## Um cuidado que não pode ser ignorado

Diferente de um container ou de uma imagem, que podem ser recriados facilmente, um volume removido leva os dados junto, sem volta. Por isso, antes de rodar qualquer comando de remoção em massa, vale conferir cuidadosamente a lista de volumes que seriam afetados, e, para volumes com dados que ainda possam ter algum valor, fazer um backup antes, como apresentado no arquivo sobre [backup e restauração de volumes](29-backup-restauracao-volumes.md).

Vale registrar também que o comando mais amplo, `docker system prune`, não remove volumes por padrão, justamente para evitar perda acidental de dados. Só acrescentando explicitamente a opção `--volumes` é que ele inclui os volumes na limpeza geral, uma escolha de design deliberada, que reforça a mesma ideia: apagar dados persistentes nunca deveria acontecer por descuido.

## Fontes

- [Docker volume cleanup: finding and removing orphaned volumes, Netdata](https://www.netdata.cloud/guides/docker/docker-volume-cleanup/)
- [How To Clean Up Docker With Prune Commands Guide, Vultr](https://docs.vultr.com/how-to-clean-up-docker-with-prune)
- [Docker Cleanup: Complete Guide to Removing Containers, Images & Volumes, Middleware](https://middleware.io/blog/docker-cleanup/)
