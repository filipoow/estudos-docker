# Criando e gerenciando redes: docker network create, inspect e connect

## Um grupo de comandos próprio, como para volumes

Assim como existe um conjunto de comandos dedicado a volumes, apresentado no arquivo sobre [criação e gerenciamento de volumes](26-criando-gerenciando-volumes.md), redes têm seu próprio grupo: `docker network`. Este arquivo apresenta os principais.

## Criando uma rede: docker network create

```
docker network create minha-rede
```

Cria uma rede bridge definida pelo usuário, com o nome `minha-rede`. Como visto no arquivo sobre [redes customizadas e DNS interno](35-redes-customizadas-dns-interno.md), essa rede já nasce com resolução de nomes automática entre os containers conectados a ela. O driver padrão é o bridge, mas é possível escolher outro explicitamente:

```
docker network create --driver bridge minha-rede
```

Também é possível definir a faixa de endereços usada pela rede, útil para evitar conflitos com outras redes já existentes na infraestrutura:

```
docker network create \
  --driver bridge \
  --subnet 172.20.0.0/16 \
  --gateway 172.20.0.1 \
  minha-rede
```

## Listando redes: docker network ls

```
docker network ls
```

Mostra todas as redes existentes na máquina, incluindo as três criadas automaticamente pelo Docker: `bridge`, `host` e `none`, correspondendo aos drivers apresentados no arquivo sobre [drivers de rede](34-drivers-de-rede-bridge-host-none-overlay.md).

## Inspecionando uma rede: docker network inspect

```
docker network inspect minha-rede
```

Devolve, em formato JSON, os detalhes completos da rede: o driver, a sub-rede e o gateway, e, principalmente, a lista de containers atualmente conectados a ela, com o endereço IP de cada um. É a ferramenta central para entender o estado de uma rede, e, como será visto no arquivo sobre [troubleshooting](39-troubleshooting-rede-containers.md), costuma ser o primeiro passo ao investigar um problema de conectividade.

## Conectando um container a uma rede: docker network connect

```
docker network connect minha-rede meu-container
```

Conecta um container que já está em execução a uma rede existente, sem precisar pará-lo. O Docker atribui automaticamente um endereço IP da sub-rede daquela rede ao container. Um mesmo container pode estar conectado a várias redes ao mesmo tempo, o que é útil, por exemplo, para um container que precisa ser alcançado por uma rede interna de serviços e, ao mesmo tempo, por uma rede voltada ao mundo externo.

## Desconectando: docker network disconnect

```
docker network disconnect minha-rede meu-container
```

Faz o caminho inverso: remove o container daquela rede, sem afetar suas conexões a outras redes nem o container em si. Esse comando é importante também na hora de remover uma rede que ainda tem containers conectados, tema do arquivo sobre [removendo containers e redes](40-removendo-containers-e-redes.md).

## Fontes

- [docker network create, Docker Docs](https://docs.docker.com/reference/cli/docker/network/create/)
- [docker network connect, Docker Docs](https://docs.docker.com/reference/cli/docker/network/connect/)
- [How to Use docker network Commands Effectively, oneuptime](https://oneuptime.com/blog/post/2026-02-08-how-to-use-docker-network-commands-effectively/view)
