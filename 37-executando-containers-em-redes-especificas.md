# Executando containers em redes específicas

## Conectando já na criação

O arquivo anterior, sobre [docker network create, inspect e connect](36-docker-network-create-inspect-connect.md), mostrou como conectar um container já existente a uma rede. O caminho mais comum, porém, é mais direto: indicar a rede no próprio momento de criar o container, com a opção `--network` do `docker run`.

## Combinando nome, rede e segundo plano

```
docker run -d --name meu-banco --network minha-rede postgres
```

Cada parte desse comando já foi apresentada separadamente neste repositório, e vale vê-las juntas:

- `-d` executa o container em segundo plano, como apresentado no arquivo sobre [modo interativo e background](13-modo-interativo-e-background.md), devolvendo o terminal imediatamente.
- `--name meu-banco` dá um nome fixo ao container. Isso é especialmente importante em redes definidas pelo usuário, já que, como visto no arquivo sobre [DNS interno](35-redes-customizadas-dns-interno.md), é esse nome que outros containers da rede vão usar para encontrá-lo.
- `--network minha-rede` conecta o container, já na criação, à rede indicada, que precisa existir antes, criada com `docker network create`.

Sem `--name`, o Docker gera um nome aleatório para o container, e sem `--network`, ele é conectado à rede bridge padrão, com todas as limitações de resolução de nomes já discutidas.

## Montando uma aplicação em duas partes

Um exemplo prático, juntando uma aplicação e seu banco de dados na mesma rede:

```
docker network create app-rede

docker run -d --name db --network app-rede \
  -e POSTGRES_PASSWORD=senha123 postgres

docker run -d --name app --network app-rede \
  -p 8080:5000 minha-app
```

Repare como o container `app` pode, a partir daí, ser configurado para acessar o banco simplesmente em `db:5432`, sem nenhum endereço IP escrito em lugar nenhum. As variáveis de ambiente usadas para configurar o banco, como `POSTGRES_PASSWORD`, seguem o mecanismo apresentado no arquivo sobre [variáveis de ambiente com ENV](19-variaveis-ambiente-env.md). E só o container `app` publica uma porta para fora, com `-p`, como apresentado no arquivo sobre [gestão de portas](20-gestao-de-portas-expose.md): o banco de dados permanece acessível apenas dentro da rede interna, sem nenhuma porta exposta ao host.

## Outros modos de rede com --network

A mesma opção `--network` aceita também os nomes dos drivers especiais já apresentados no arquivo sobre [drivers de rede](34-drivers-de-rede-bridge-host-none-overlay.md):

```
docker run -d --network host nginx
docker run -d --network none alpine sleep 3600
```

O primeiro compartilha a rede do host, sem isolamento. O segundo desliga completamente a rede do container.

## Fontes

- [How to Create Custom Docker Networks, oneuptime](https://oneuptime.com/blog/post/2026-01-30-docker-custom-networks/view)
- [How to Set Up Docker Container Networking Modes, oneuptime](https://oneuptime.com/blog/post/2026-01-25-docker-container-networking-modes/view)
- [Docker Container Networking: DNS and Custom Networks, Easton Dev](https://eastondev.com/blog/en/posts/dev/20251217-docker-container-networking/)
