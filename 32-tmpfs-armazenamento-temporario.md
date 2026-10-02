# Utilizando tmpfs para armazenamento temporário de dados

## Quando persistir não é o objetivo

Todo o restante deste bloco de aulas girou em torno de fazer dados sobreviverem. O tmpfs, apresentado de forma resumida no arquivo sobre [tipos de mounts](27-tipos-de-mounts.md), faz exatamente o contrário, de propósito: ele guarda dados que deveriam desaparecer assim que o container parar.

## O que é um tmpfs mount

Um tmpfs é um sistema de arquivos que vive na memória RAM da máquina, em vez de no disco. Quando um tmpfs mount é criado dentro de um container, aquele caminho passa a ser armazenado só em memória. Os dados existem enquanto o container roda, e simplesmente deixam de existir quando ele para, sem nenhuma possibilidade de recuperação depois. Os dados também não são gravados na camada de escrita do container, apresentada no arquivo sobre [volumes e persistência](25-volumes-persistencia-de-dados.md).

## Como usar

```
docker run -d \
  --mount type=tmpfs,target=/app/cache \
  minha-imagem
```

Seguindo a mesma sintaxe da opção `--mount`, apresentada no arquivo sobre [mapeamento de volumes](28-mapeando-volumes-mount.md), o tipo é `tmpfs` e só o destino é necessário, já que não existe uma origem: não há volume nem pasta do host sendo conectada. Também é possível limitar o tamanho máximo de memória que aquele tmpfs pode usar:

```
--mount type=tmpfs,target=/app/cache,tmpfs-size=512m
```

Sem esse limite, o padrão é permitir que o tmpfs use até metade da memória RAM do host, o que pode ser muito mais do que o desejado.

## Para que serve

**Velocidade**: leitura e escrita em memória são ordens de grandeza mais rápidas do que em qualquer disco, mesmo um SSD veloz. Para cache temporário, ou arquivos de trabalho intermediários que são lidos e escritos intensamente, isso pode fazer diferença real de desempenho.

**Menos desgaste de disco**: para dados que mudam constantemente e não precisam ser guardados, como arquivos de sessão ou de bloqueio, escrever no disco só gera desgaste desnecessário, especialmente em SSDs.

**Segurança**: talvez o uso mais importante. Dados sensíveis temporários, como uma chave de API ou credencial recebida só em tempo de execução, não deveriam tocar o disco, nem mesmo dentro de um volume. Guardados em tmpfs, desaparecem junto com o container, sem deixar rastro físico no disco. Isso complementa as práticas de [segurança e gestão de dados persistentes](30-seguranca-gestao-dados-persistentes.md).

## Limitações a ter em mente

O tmpfs funciona apenas em hosts Linux, já que depende de um recurso do kernel, relacionado à estrutura de sistema operacional apresentada no arquivo sobre [containers e sistema operacional](08-containers-e-sistema-operacional.md). Como usa memória RAM, ele disputa esse recurso com a própria aplicação e com outros containers, então vale dimensionar com cuidado. E, por definição, não serve para nada que precise ser guardado: usar tmpfs para algo que deveria persistir significa perder aquilo na próxima parada do container.

## Fechando o bloco

Os três tipos de mount cobrem, juntos, três necessidades diferentes: named volumes para dados que pertencem à aplicação e precisam persistir, bind mounts para arquivos que pertencem ao host e precisam ser compartilhados, e tmpfs para dados que não deveriam sobreviver ao container. Escolher o tipo certo para cada dado é, no fundo, decidir com clareza quanto tempo aquele dado deveria existir.

## Fontes

- [How to Use Docker tmpfs Mounts for Faster I/O, oneuptime](https://oneuptime.com/blog/post/2026-01-16-docker-tmpfs-mounts/view)
- [Docker tmpfs Mounts: The Complete Guide to In-Memory Container Storage, DevOps.dev](https://blog.devops.dev/docker-tmpfs-mounts-the-complete-guide-to-in-memory-container-storage-6b3e901bf2f6)
- [Docker Mount Types: Volumes, Bind Mounts & tmpfs Guide, DataCamp](https://www.datacamp.com/tutorial/docker-mount)
