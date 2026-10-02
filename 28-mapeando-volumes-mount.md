# Mapeando volumes para containers com a opção --mount

## Conectando um volume na hora de rodar o container

Criar um volume, como visto no arquivo sobre [criação e gerenciamento de volumes](26-criando-gerenciando-volumes.md), é só metade do trabalho. A outra metade é conectar esse volume a um container específico, dizendo em qual pasta interna do container aquele armazenamento deve aparecer. Isso é feito durante o `docker run`, já apresentado no arquivo sobre [comandos básicos do Docker CLI](09-docker-cli-comandos-basicos.md), com a opção `--mount`.

## A sintaxe do --mount

```
docker run -d \
  --mount type=volume,source=meus-dados,target=/var/lib/postgresql/data \
  postgres
```

A opção `--mount` recebe uma lista de pares chave e valor, separados por vírgula, cada um deixando explícito o que representa:

- `type` define o tipo de mount, apresentados no arquivo sobre [tipos de mounts](27-tipos-de-mounts.md): `volume`, `bind` ou `tmpfs`.
- `source` indica a origem: o nome do volume (no caso de um named volume) ou o caminho da pasta no host (no caso de um bind mount).
- `target` indica o destino: o caminho dentro do container onde aquele armazenamento deve aparecer.

Nesse exemplo, tudo que o PostgreSQL escrever em `/var/lib/postgresql/data`, a pasta onde ele guarda seus dados internamente, na verdade vai parar dentro do volume `meus-dados`, sobrevivendo mesmo que o container seja removido e recriado depois.

## O comportamento ao usar um volume vazio pela primeira vez

Um detalhe útil: quando um named volume vazio é conectado a um container, e o caminho de destino já tinha conteúdo na imagem original, o Docker copia esse conteúdo inicial para dentro do volume na primeira vez. É esse comportamento que permite, por exemplo, que imagens oficiais de bancos de dados já venham com uma estrutura inicial de pastas pronta, sem exigir nenhuma preparação manual do volume antes.

## A alternativa mais curta: a opção -v

Existe também uma sintaxe mais antiga e mais compacta, a opção `-v` (ou `--volume`):

```
docker run -d -v meus-dados:/var/lib/postgresql/data postgres
```

Ela faz exatamente a mesma coisa, só que usa uma string única separada por dois-pontos em vez de pares explícitos. A documentação oficial do Docker recomenda `--mount` em vez de `-v`, justamente por ser mais explícita e mais legível, principalmente quando se acrescentam opções extras, como acesso somente leitura ou drivers de volume específicos, casos em que a sintaxe do `-v` rapidamente se torna uma sequência difícil de interpretar.

## Montando em modo somente leitura

Uma das opções extras mais úteis do `--mount` é o `readonly`, que impede o container de modificar o conteúdo do volume:

```
--mount type=volume,source=configuracoes,target=/etc/app,readonly
```

Essa opção volta a aparecer no arquivo sobre [segurança e gestão de dados persistentes](30-seguranca-gestao-dados-persistentes.md), por ser uma das defesas mais simples e mais eficazes ao lidar com dados compartilhados entre containers.

## Fontes

- [Docker Mount Types: Volumes, Bind Mounts & tmpfs Guide, DataCamp](https://www.datacamp.com/tutorial/docker-mount)
- [Docker mount revisited, rednafi](https://rednafi.com/misc/docker_mount/)
- [Introduction to Docker Bind Mounts and Volumes, 4sysops](https://4sysops.com/archives/introduction-to-docker-bind-mounts-and-volumes/)
