# Visualizando, iniciando, pausando, parando e reiniciando containers

## Controlando cada transição de estado manualmente

O arquivo sobre o [ciclo de vida de um container](10-ciclo-de-vida-container.md) apresentou os estados possíveis. Este arquivo apresenta os comandos específicos usados para mover um container de um estado para outro, de forma controlada.

## Visualizando: docker ps

Já apresentado no arquivo sobre [comandos básicos](09-docker-cli-comandos-basicos.md), o `docker ps` é o ponto de partida de qualquer gerenciamento: antes de agir sobre um container, é preciso saber qual é o nome ou ID dele, e em qual estado ele está.

```
docker ps -a
```

## Iniciando: docker start

```
docker start meu-app
```

Inicia um container que está parado (Exited) ou que ainda nem chegou a rodar (Created), levando-o ao estado Running. Diferente do `docker run`, apresentado no arquivo sobre [docker create e docker run](11-docker-create-vs-run.md), o `docker start` não cria nada novo, ele só religa um container que já existe.

## Pausando e retomando: docker pause e docker unpause

```
docker pause meu-app
docker unpause meu-app
```

O `docker pause` congela todos os processos dentro do container, sem liberar nenhum recurso já alocado, levando-o ao estado Paused já apresentado no arquivo sobre o ciclo de vida. O `docker unpause` reverte isso, retomando exatamente de onde parou. É uma ferramenta útil para situações pontuais, como liberar temporariamente processador para outra tarefa urgente na mesma máquina, sem perder o estado interno daquele container.

## Parando: docker stop

```
docker stop meu-app
```

Encerra o container de forma organizada: o Docker primeiro envia um sinal de encerramento suave (SIGTERM), dando um tempo para o processo principal finalizar tarefas pendentes, e só envia um sinal de encerramento forçado (SIGKILL) se o processo não parar sozinho dentro de um prazo padrão. Esse comportamento é o mesmo conceito de sinais já apresentado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/07-processamento-texto-e-automacao/06-processos-pgrep-pkill-xargs-exit-codes.md).

## Reiniciando: docker restart

```
docker restart meu-app
```

Combina um `docker stop` seguido de um `docker start`, útil quando é preciso que uma aplicação recarregue completamente sua configuração ou seu estado interno, sem precisar recriar o container do zero.

## Agindo sobre vários containers de uma vez

Todos esses comandos aceitam mais de um nome ou ID por vez, separados por espaço:

```
docker restart web-app banco-de-dados cache
```

Também é comum combinar esses comandos com a saída do próprio `docker ps`, usando substituição de comando, já apresentada no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/07-processamento-texto-e-automacao/02-cut-awk-tr-manipulacao-dados.md), para agir sobre um grupo inteiro de containers de uma vez, por exemplo parando todos os containers em execução:

```
docker stop $(docker ps -q)
```

## Fontes

- [Docker Commands, Part 3, DEV Community](https://dev.to/meghasharmaaaa/docker-commands-part-3-2ofl)
- [How to Pause and Unpause Docker Containers, oneuptime](https://oneuptime.com/blog/post/2026-02-08-how-to-pause-and-unpause-docker-containers/view)
- [docker container pause, Docker Docs](https://docs.docker.com/reference/cli/docker/container/pause/)
