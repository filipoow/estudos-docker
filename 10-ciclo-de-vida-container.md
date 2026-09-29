# O ciclo de vida de um container: os estados principais

## Um container não é só "ligado" ou "desligado"

Depois de conhecer os comandos básicos apresentados no [arquivo anterior](09-docker-cli-comandos-basicos.md), vale entender que um container Docker passa por vários estados diferentes durante sua existência, e não só uma alternância simples entre rodando e parado. Entender esses estados ajuda bastante a interpretar corretamente a saída do `docker ps`, e a diagnosticar comportamentos inesperados.

## Created: existe, mas nunca rodou

Um container entra no estado Created quando o Docker já criou a estrutura dele a partir de uma imagem, mas ainda não iniciou o processo principal daquele container. É o estado resultante de um `docker create`, apresentado em detalhe no arquivo sobre [docker create e docker run](11-docker-create-vs-run.md), antes de qualquer `docker start` ser executado.

## Running: em plena execução

O estado Running é o que a maioria das pessoas imagina quando pensa num container: o processo principal está de fato em execução, consumindo processador e memória normalmente, exatamente como qualquer outro processo comum do sistema, isolado pelos mecanismos de namespaces e cgroups já apresentados no arquivo correspondente.

## Paused: congelado, mas ainda em memória

Um container passa para o estado Paused quando seus processos são congelados através do comando `docker pause`. Diferente de parar o container, pausar não libera nenhum recurso, memória, processador ou rede que já estava alocado, ele simplesmente suspende a execução, como se o tempo tivesse parado só para aqueles processos específicos. O comando `docker unpause` reverte esse estado, retomando a execução exatamente de onde parou.

## Restarting: um estado de transição

O estado Restarting indica que o container está no meio de um processo de reinício, seja porque alguém rodou `docker restart` manualmente, seja porque uma política de reinício automático (parecida com o `Restart=on-failure` já apresentado no arquivo sobre [systemd](https://github.com/filipoow/estudos-linux/blob/main/10-devops-monitoramento-e-processos-longos/07-systemd-servicos-personalizados.md) do meu outro repositório) detectou uma falha e está tentando religar o container sozinha.

## Exited: terminou, mas ainda existe

Quando o processo principal dentro do container termina, seja porque completou seu trabalho, seja por um erro, o container passa para o estado Exited. Nesse estado, nenhum processador ou memória é mais consumido, mas o container em si ainda existe no disco, junto com seu sistema de arquivos e seus logs, até que alguém o remova explicitamente com `docker rm`, apresentado no arquivo sobre [removendo containers](15-removendo-containers.md).

## Dead: um estado de falha na própria remoção

O estado Dead é o mais raro, e o menos desejável: acontece quando o Docker tenta parar ou remover um container, mas essa operação falha de um jeito que deixa o container preso, sem conseguir rodar novamente, mas também sem conseguir ser limpo automaticamente pelo próprio Docker. Costuma exigir intervenção manual, ou até reiniciar o próprio daemon, para resolver.

## Por que esses estados importam na prática

Boa parte do trabalho de diagnosticar um problema com containers passa por identificar em qual desses estados ele está. Um container preso em Restarting repetidamente, por exemplo, geralmente indica que a aplicação dentro dele está falhando logo na inicialização, e vale a pena consultar seus logs para investigar. Já um container em Exited pode simplesmente significar que ele cumpriu seu propósito e terminou normalmente, ou pode indicar um erro, dependendo do código de saída, o mesmo conceito já apresentado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/07-processamento-texto-e-automacao/06-processos-pgrep-pkill-xargs-exit-codes.md).

## Fontes

- [States of a Docker Container, Baeldung on Ops](https://www.baeldung.com/ops/docker-container-states)
- [The Complete Docker Container Lifecycle, DEV Community](https://dev.to/srinivasamcjf/the-complete-docker-container-lifecycle-states-namespaces-and-internal-workings-4o78)
- [Docker Container Lifecycle: Key States and Best Practices, Last9](https://last9.io/blog/docker-container-lifecycle/)
