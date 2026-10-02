# Backup e restauração de volumes

## Persistência não é o mesmo que proteção

Um volume garante que os dados sobrevivam à remoção de um container, como apresentado no arquivo sobre [volumes e persistência](25-volumes-persistencia-de-dados.md), mas não garante nada contra outros riscos: um volume apagado por engano, um disco que falha, ou uma atualização que corrompe dados. Por isso, qualquer volume com dados importantes precisa de uma estratégia de backup, o mesmo princípio de manutenção automatizada já apresentado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/04-pacotes-scripts-automacao/04-crontab-agendamento.md).

## A ideia central: um container temporário que lê o volume

O Docker não tem um comando `docker volume backup` pronto. A técnica padrão usa um container descartável, só para essa tarefa, que monta ao mesmo tempo o volume a ser copiado e uma pasta do host onde o arquivo de backup será guardado, e usa o `tar` para compactar tudo. Esse container é criado, executa a tarefa, e desaparece sozinho, uma aplicação direta da natureza efêmera de containers, apresentada no arquivo correspondente.

## Fazendo o backup

```
docker run --rm \
  -v meus-dados:/volume \
  -v $(pwd):/backup \
  alpine tar -czf /backup/backup-meus-dados.tar.gz -C /volume .
```

Lendo por partes: `--rm` remove o container assim que ele terminar, sem deixar lixo para trás, como visto no arquivo sobre [removendo containers](15-removendo-containers.md). Os dois `-v` conectam o volume (visto dentro do container como `/volume`) e a pasta atual do host (vista como `/backup`). A imagem `alpine`, uma distribuição Linux minúscula, é só um veículo para ter o comando `tar` disponível. O `tar -czf` cria um arquivo compactado dentro de `/backup`, ou seja, na pasta atual do host, contendo todo o conteúdo do volume.

## Restaurando o backup

```
docker run --rm \
  -v meus-dados:/volume \
  -v $(pwd):/backup \
  alpine sh -c "tar -xzf /backup/backup-meus-dados.tar.gz -C /volume"
```

O processo é o inverso: um container temporário monta o volume de destino e a pasta com o arquivo de backup, e extrai o conteúdo compactado para dentro do volume. Para restaurar em um volume novo, basta criá-lo antes, com `docker volume create`, apresentado no arquivo sobre [criação e gerenciamento de volumes](26-criando-gerenciando-volumes.md).

## Um cuidado importante: pare os containers antes

Se o volume estiver sendo escrito por um container em execução, como um banco de dados, a cópia pode capturar os dados num estado inconsistente, no meio de uma operação de escrita. A recomendação é parar os containers que usam aquele volume antes de fazer o backup ou a restauração, usando o `docker stop` apresentado no arquivo sobre [gerenciamento de containers](12-gerenciando-containers-ps-start-stop-restart.md), e só depois religá-los. Alguns bancos de dados oferecem ferramentas próprias de backup consistente, capazes de funcionar mesmo com o banco ativo, mas a regra geral para uma cópia direta do volume é garantir que nada esteja escrevendo nele no momento.

## Automatizando

Como um backup que depende de alguém lembrar de rodá-lo manualmente acaba sendo esquecido, o passo natural é encapsular esses comandos num script e agendá-lo, combinando os conceitos de [scripts em shell](https://github.com/filipoow/estudos-linux/blob/main/04-pacotes-scripts-automacao/03-scripts-shell.md) e [CronTab](https://github.com/filipoow/estudos-linux/blob/main/04-pacotes-scripts-automacao/04-crontab-agendamento.md) já apresentados no outro repositório.

## Fontes

- [How to Back Up and Restore Docker Volumes, oneuptime](https://oneuptime.com/blog/post/2026-01-06-docker-volume-backup-restore/view)
- [Backup Docker Container With Its Data Volumes, Baeldung on Ops](https://www.baeldung.com/ops/docker-backup-container-data-volumes)
- [How to Backup Docker Volumes, GeeksforGeeks](https://www.geeksforgeeks.org/devops/how-to-backup-docker-volumes/)
