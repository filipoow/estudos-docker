# Os drivers de rede do Docker: bridge, host, none e overlay

## Um driver define o comportamento da rede

Toda rede Docker é criada com um driver, que determina como ela funciona por baixo dos panos. O arquivo sobre a [importância das Docker Networks](33-docker-networks-importancia-isolamento.md) apresentou o conceito geral de isolamento por namespaces. Aqui estão os quatro drivers mais importantes, e quando cada um faz sentido.

## bridge: o padrão para um único host

O driver bridge é o padrão do Docker. Ele cria uma rede privada interna, na máquina hospedeira, à qual os containers se conectam, e containers na mesma bridge conseguem se comunicar entre si, enquanto ficam isolados dos de outras redes.

Existem dois tipos de rede bridge: a rede padrão, criada automaticamente pelo Docker, chamada `bridge` (ou `docker0`), e redes bridge definidas pelo usuário. A diferença entre elas, em especial na resolução de nomes, é aprofundada no arquivo sobre [redes customizadas e DNS interno](35-redes-customizadas-dns-interno.md). Para a esmagadora maioria dos cenários de uma única máquina, como desenvolvimento local ou aplicações simples com poucos serviços, uma bridge definida pelo usuário é a escolha recomendada.

## host: sem isolamento de rede

No driver host, o container deixa de ter um namespace de rede próprio e passa a compartilhar diretamente a pilha de rede do host. Não existe tradução de endereços, nem mapeamento de portas: se a aplicação dentro do container escuta na porta 80, ela está escutando diretamente na porta 80 da máquina hospedeira.

A vantagem é desempenho, já que elimina a camada de tradução entre o container e a rede real. A desvantagem é a perda total de isolamento de rede, além do risco de conflito de portas com outros serviços do próprio host. Costuma ser usado em casos específicos, como ferramentas de monitoramento que precisam enxergar a rede da máquina como ela é, por exemplo o Node Exporter apresentado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/10-devops-monitoramento-e-processos-longos/04-node-exporter-scraping-prometheus.md), ou aplicações com requisitos de latência muito baixos.

## none: isolamento total

O driver none desliga a rede do container completamente. Ele só tem a interface de loopback interna, sem nenhuma conexão com o host, com outros containers ou com a internet. É o oposto do driver host, e serve para cargas de trabalho que processam dados locais e não deveriam, em hipótese alguma, ter acesso à rede, como tarefas em lote com dados sensíveis, onde cortar a rede é uma camada extra de segurança.

## overlay: redes que atravessam vários hosts

Todos os drivers anteriores funcionam dentro de uma única máquina. O driver overlay resolve um problema diferente: conectar containers que rodam em máquinas diferentes, como se estivessem todos na mesma rede. Ele cria uma rede virtual distribuída por cima da rede física entre os hosts, e é a base de comunicação em ambientes de orquestração, como o Docker Swarm. Num contexto de Kubernetes, apresentado no arquivo sobre [orquestração de containers](05-kubernetes-orquestracao.md), a comunicação entre máquinas segue outros mecanismos próprios, mas a ideia por trás é parecida: abstrair a rede física para que os containers conversem sem se preocupar com onde cada um está rodando.

## Um quinto driver, para casos específicos: macvlan

Vale mencionar, ainda que fora do escopo principal, o driver macvlan, que dá a cada container seu próprio endereço MAC e o conecta diretamente à rede física, como se fosse um dispositivo independente da rede local. É útil principalmente para aplicações legadas que esperam estar diretamente na rede física, ou dispositivos que precisam ser descobertos na rede local.

## Como escolher

Uma regra prática simples cobre a maior parte dos casos: bridge definida pelo usuário para aplicações em um único host, host só quando o desempenho de rede ou a visibilidade direta da rede do host for realmente necessária, none quando o container não deve ter nenhum acesso à rede, e overlay quando a aplicação se espalha por várias máquinas.

## Fontes

- [Network drivers, Docker Docs](https://docs.docker.com/engine/network/drivers/)
- [Docker Networking Explained: Bridge, Host, Overlay, and Macvlan with Real Examples, The Basic Tech Info](https://www.thebasictechinfo.com/docker-and-kubernetes/docker-networking-explained-bridge-host-overlay-and-macvlan-with-real-examples/)
- [Docker Network Drivers: Explanation & Comparison, Jun's Learning](https://exia.dev/blog/2025-08-13/Docker-Network-Drivers-Explanation--Comparison/)
