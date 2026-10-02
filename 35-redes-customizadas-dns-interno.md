# Redes customizadas e resolução de nomes via DNS interno

## O problema da rede bridge padrão

O arquivo sobre os [drivers de rede](34-drivers-de-rede-bridge-host-none-overlay.md) mencionou que existem dois tipos de rede bridge. A rede padrão, criada automaticamente pelo Docker, tem uma limitação que costuma surpreender quem está começando: containers conectados a ela só conseguem se comunicar por endereço IP, não por nome.

Isso é um problema real. Endereços IP de containers são atribuídos dinamicamente, e mudam toda vez que um container é recriado, como esperado de algo descartável, conforme apresentado no arquivo sobre a [natureza efêmera dos containers](16-natureza-efemera-containers.md). Escrever o IP de um banco de dados dentro da configuração de uma aplicação funcionaria só até o próximo `docker run`.

## Redes definidas pelo usuário trazem DNS embutido

Quando uma rede bridge é criada pelo usuário, o Docker ativa, para ela, um servidor DNS embutido. Todo container conectado a essa rede passa a poder resolver o nome de qualquer outro container da mesma rede, simplesmente usando o nome dele como se fosse um endereço de site. Dentro de cada container, esse servidor DNS aparece no endereço `127.0.0.11`, e ele repassa consultas por nomes externos aos resolvedores configurados no host.

Na prática, isso significa que uma aplicação pode ser configurada para se conectar a um banco de dados usando simplesmente o nome do container, como `db`, em vez de um IP, e essa configuração continua funcionando mesmo se o container do banco for destruído e recriado, já que o nome permanece o mesmo, e o Docker mantém a tradução atualizada.

## Mais vantagens além do DNS

Redes definidas pelo usuário trazem outros benefícios sobre a bridge padrão. O isolamento é melhor: só containers explicitamente conectados àquela rede conseguem se alcançar, em vez de todos os containers da máquina compartilharem a mesma rede padrão por default. É possível escolher faixas de endereços e sub-redes próprias, evitando conflito com outras redes já existentes na infraestrutura. E containers podem ser conectados e desconectados de redes dinamicamente, sem precisar parar e recriar o container, tema apresentado no [próximo arquivo](36-docker-network-create-inspect-connect.md).

## Aliases de rede

Além do nome do próprio container, é possível dar a ele um ou mais aliases dentro de uma rede, nomes alternativos pelos quais ele também pode ser encontrado. Isso é útil, por exemplo, quando uma aplicação espera se conectar a um host chamado `database`, independente de como o container do banco foi nomeado ao ser criado.

## A regra prática

Para qualquer cenário com mais de um container que precise conversar entre si numa mesma máquina, a recomendação é criar uma rede própria em vez de usar a rede padrão. É uma mudança pequena, um único comando extra, que resolve toda uma classe de problemas de configuração, e que combina com a prática de nomear containers de forma deliberada, ao iniciá-los, como apresentado no arquivo sobre [executando containers em redes específicas](37-executando-containers-em-redes-especificas.md).

## Fontes

- [Bridge network driver, Docker Docs](https://docs.docker.com/engine/network/drivers/bridge/)
- [Docker Networking, Demystified: Bridge, Host, and Container DNS, DEV Community](https://dev.to/jjoyneriv/docker-networking-demystified-bridge-host-and-container-dns-5fac)
- [Troubleshooting Docker DNS: Why Container Names Don't Resolve, oneuptime](https://oneuptime.com/blog/post/2026-01-06-docker-dns-troubleshooting/view)
