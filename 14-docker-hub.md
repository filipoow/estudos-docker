# A importância do Docker Hub para armazenar e compartilhar imagens

## Um repositório de imagens, no mesmo espírito de um repositório de código

Assim como o GitHub, apresentado no [meu outro repositório de estudos de Git e GitHub](https://github.com/filipoow/estudos-git-github/blob/main/03-diferenca-git-github.md), serve como ponto central para compartilhar código versionado com Git, o Docker Hub cumpre esse mesmo papel para imagens de container: é um registro (registry) na nuvem, onde imagens podem ser guardadas, versionadas e compartilhadas publicamente ou de forma privada.

## Por que ele é tão central no ecossistema Docker

Sempre que um comando como `docker run nginx`, já apresentado no arquivo sobre [comandos básicos do Docker CLI](09-docker-cli-comandos-basicos.md), é executado sem que a imagem `nginx` já exista localmente, é do Docker Hub que ela é baixada automaticamente, por padrão. O Docker Hub reúne milhares de imagens oficiais e mantidas pela comunidade, cobrindo praticamente qualquer software popular, bancos de dados, servidores web, linguagens de programação, prontas para uso imediato, sem precisar construir uma imagem do zero para tarefas comuns.

## Baixando uma imagem: docker pull

```
docker pull postgres
```

Esse comando busca a imagem `postgres` diretamente do Docker Hub, guardando-a localmente para uso futuro. Vale notar que o próprio `docker run` já faz esse pull automaticamente quando necessário, então rodar `docker pull` manualmente costuma servir para baixar uma imagem de antemão, sem já iniciar um container a partir dela.

## Compartilhando uma imagem própria: docker push

Depois de construir uma imagem própria (usando um Dockerfile, já apresentado no arquivo sobre [Docker e Golang](https://github.com/filipoow/estudos-linux/blob/main/07-processamento-texto-e-automacao/08-docker-golang-processamento-logs.md) do meu outro repositório de estudos de Linux), é possível publicá-la no Docker Hub, tornando-a acessível para outras máquinas ou outras pessoas.

```
docker login
docker tag minha-app usuario/minha-app:1.0
docker push usuario/minha-app:1.0
```

O `docker login` autentica com uma conta do Docker Hub. O `docker tag` renomeia (ou cria um apelido para) a imagem local, seguindo o formato esperado pelo registro, geralmente `usuario/nome-da-imagem:versao`, retomando o conceito de tags de versão já apresentado no arquivo sobre [imagens versionadas](03-imagens-versionadas.md). Só depois disso o `docker push` de fato envia a imagem para o Docker Hub.

## Público ou privado, a escolha de quem publica

Assim como um repositório no GitHub, uma imagem no Docker Hub pode ser pública, visível e utilizável por qualquer pessoa, ou privada, restrita a uma conta ou equipe específica. Essa flexibilidade é o que permite ao Docker Hub servir tanto como uma biblioteca aberta de ferramentas prontas para uso, quanto como um repositório interno de imagens proprietárias de uma empresa, usadas só dentro de sua própria infraestrutura.

## Fontes

- [Docker Hub Container Image Library](https://hub.docker.com/)
- [How Do You Push and Pull Images from Docker Hub?, Medium](https://medium.com/@haroldfinch01/how-do-you-push-and-pull-images-from-docker-hub-42bae39c3ff7)
- [docker image push, Docker Docs](https://docs.docker.com/reference/cli/docker/image/push/)
