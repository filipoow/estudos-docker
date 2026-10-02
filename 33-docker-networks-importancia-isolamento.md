# A importância das Docker Networks e o isolamento por namespaces de rede

## Containers precisam conversar, mas só com quem deveriam

Um container isolado, sem nenhuma forma de se comunicar, tem pouca utilidade prática: uma aplicação web precisa falar com seu banco de dados, e usuários precisam alcançar essa aplicação de fora. Ao mesmo tempo, containers que não têm nada a ver uns com os outros não deveriam enxergar o tráfego de rede um do outro. O sistema de redes do Docker existe para equilibrar exatamente essas duas necessidades: comunicação controlada e isolamento por padrão.

## Cada container tem sua própria pilha de rede

O arquivo sobre [namespaces e cgroups](02-namespaces-e-cgroups.md) já apresentou, entre os tipos de namespace usados pelo Docker, o namespace de rede. Aqui ele ganha protagonismo: cada container recebe o seu próprio namespace de rede, uma pilha de rede completa e privada, com suas próprias interfaces, seu próprio endereço IP e sua própria tabela de rotas. Um processo dentro de um container não consegue ver as interfaces de rede de outro container, nem as do host, a menos que o Docker o conecte deliberadamente a elas.

## Como as peças se conectam: veth e bridge

Para que um container isolado ainda consiga se comunicar, o Docker cria, para cada um, um par de interfaces virtuais de rede, chamadas veth. Uma ponta do par fica dentro do namespace de rede do container, funcionando como a placa de rede dele. A outra ponta fica do lado de fora, no host, ligada a uma interface virtual chamada bridge (ponte), que funciona como um switch virtual conectando todos os containers daquela rede. Por padrão, o Docker cria uma bridge chamada `docker0`, em máquinas Linux, e atribui aos containers endereços numa faixa privada, normalmente começando em 172.17.

## Três tipos de comunicação

As Docker Networks cobrem, na prática, três cenários diferentes de comunicação:

- **Entre containers**: containers conectados à mesma rede conseguem se comunicar diretamente, e ficam isolados de containers conectados a redes diferentes.
- **Com o host**: o container pode ser alcançado a partir da máquina hospedeira, normalmente através de portas publicadas, como apresentado no arquivo sobre [gestão de portas e EXPOSE](20-gestao-de-portas-expose.md).
- **Com redes externas**: o container consegue acessar a internet e outras redes, passando pelo host, que faz a tradução de endereços (NAT) de saída.

## Por que essa separação é valiosa

Esse modelo permite organizar uma aplicação em redes separadas conforme a necessidade de cada parte: um banco de dados que só deveria ser acessado pela camada de aplicação pode ficar numa rede interna, sem nenhuma exposição ao exterior, enquanto só o servidor web fica acessível de fora. É a mesma lógica de menor privilégio já aplicada a volumes, apresentada no arquivo sobre [segurança e gestão de dados persistentes](30-seguranca-gestao-dados-persistentes.md), só que agora aplicada ao tráfego de rede: cada container enxerga e alcança só o que realmente precisa.

## Fontes

- [Docker Networking: Basics, Network Types & Examples, Spacelift](https://spacelift.io/blog/docker-networking)
- [Understanding Docker Networks: A Comprehensive Guide, Better Stack](https://betterstack.com/community/guides/scaling-docker/docker-networks/)
- [Docker network and network namespaces in practice, Learn Docker](https://learn-docker.it-sziget.hu/en/latest/pages/advanced/kernel-namespaces-network.html)
