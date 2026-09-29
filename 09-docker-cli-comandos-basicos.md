# Os comandos básicos do Docker CLI para gerenciar containers

## Uma linha de comando pensada para o dia a dia

O arquivo sobre a [arquitetura cliente-servidor do Docker](06-arquitetura-cliente-servidor-docker-daemon.md) já explicou que o comando `docker` é só um cliente, que envia instruções para o daemon fazer o trabalho de fato. Este arquivo apresenta o conjunto de comandos mais usado no dia a dia, a base sobre a qual todo o restante deste bloco de aulas vai se apoiar.

## Rodando um container: `docker run`

```
docker run ubuntu
```

Esse comando cria e já inicia um container a partir da imagem `ubuntu`. Se essa imagem ainda não existir localmente, o Docker a baixa automaticamente do Docker Hub, apresentado com mais detalhe no arquivo correspondente, antes de criar o container.

## Listando containers: `docker ps`

```
docker ps
docker ps -a
```

Sozinho, `docker ps` mostra só os containers em execução no momento. Com a opção `-a` ("all"), a lista passa a incluir também os containers parados, o que é essencial para enxergar o histórico completo do que já foi criado na máquina, não só o que está ativo agora.

## Parando um container: `docker stop`

```
docker stop nome_do_container
```

Esse comando encerra um container em execução de forma organizada, dando a ele a chance de finalizar processos internos antes de parar de fato, o mesmo espírito do sinal de encerramento já apresentado em detalhe no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/07-processamento-texto-e-automacao/06-processos-pgrep-pkill-xargs-exit-codes.md).

## Removendo um container: `docker rm`

```
docker rm nome_do_container
```

Remove definitivamente um container já parado, liberando o espaço que ele ocupava. Esse comando é aprofundado no arquivo sobre [removendo containers](15-removendo-containers.md), incluindo como forçar a remoção de um container que ainda está em execução.

## Listando imagens: `docker images`

```
docker images
```

Enquanto `docker ps` mostra containers (instâncias em execução ou paradas), `docker images` mostra as imagens (os modelos), já apresentadas no arquivo sobre [imagens versionadas](03-imagens-versionadas.md), que já foram baixadas ou construídas localmente na máquina.

## Removendo uma imagem: `docker rmi`

```
docker rmi nome_da_imagem
```

Remove uma imagem do disco local. É preciso que nenhum container, mesmo parado, ainda dependa daquela imagem, o Docker recusa a remoção caso contrário, como uma proteção contra apagar acidentalmente algo ainda em uso.

## O restante deste bloco de aulas

Esses seis comandos cobrem o essencial, mas cada um deles esconde detalhes importantes, apresentados nos arquivos seguintes: os diferentes estados que um container atravessa durante sua vida, apresentados no arquivo sobre o [ciclo de vida de um container](10-ciclo-de-vida-container.md), a diferença sutil entre `docker create` e `docker run`, e as opções mais avançadas de execução interativa e em segundo plano.

## Fontes

- [CLI Cheat Sheet, Docker Docs](https://docs.docker.com/get-started/docker_cheatsheet.pdf)
- [docker container run, Docker Docs](https://docs.docker.com/reference/cli/docker/container/run/)
- [Top 20+ Essential Docker Commands, DEV Community](https://dev.to/tungbq/the-essential-docker-commands-1c23)
