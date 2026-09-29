# Namespaces e cgroups: o isolamento por trás dos containers

## Dois mecanismos do kernel, não do Docker

O arquivo anterior, sobre a [evolução dos containers](01-evolucao-chroot-ate-docker.md), já adiantou que o Docker não inventou o isolamento que sustenta seus containers. Esse isolamento vem de dois mecanismos construídos diretamente no kernel Linux: namespaces e cgroups. O Docker (assim como o LXC antes dele) só organiza e simplifica o uso dessas duas peças, que continuariam existindo e funcionando mesmo sem o Docker.

## Namespaces: controlando o que um processo enxerga

Um namespace é uma partição, em nível de kernel, de um recurso global do sistema. Quando um processo é colocado dentro de um namespace, ele passa a ter sua própria visão privada daquele recurso, enquanto processos de fora continuam vendo a visão original, completa. É esse mecanismo que faz um processo rodando dentro de um container acreditar que está sozinho numa máquina só sua, mesmo compartilhando o mesmo kernel com dezenas de outros containers.

Por padrão, o Docker cria vários namespaces diferentes para cada container:

- **PID**: cada container enxerga sua própria lista de processos, começando do seu próprio PID 1, sem ver os processos de outros containers ou do sistema hospedeiro.
- **Rede**: cada container tem sua própria interface de rede, endereço IP e tabela de rotas, isolados dos demais.
- **Mount**: cada container enxerga seu próprio sistema de arquivos, um descendente direto da ideia original do `chroot`, apresentado no arquivo sobre a [evolução dos containers](01-evolucao-chroot-ate-docker.md), só que muito mais completo.
- **UTS**: isola o nome de host (hostname), permitindo que cada container tenha seu próprio nome, independente do nome da máquina física.
- **IPC**: isola os mecanismos de comunicação entre processos, impedindo que um container se comunique diretamente com processos de outro através desses canais.

## Cgroups: controlando quanto um processo pode usar

Enquanto os namespaces controlam o que um processo enxerga, os cgroups (control groups) controlam quanto de recurso físico esse processo pode efetivamente consumir: tempo de processador, quantidade de memória, taxa de leitura e escrita em disco, entre outros.

Sem esse controle, um único container mal comportado poderia consumir toda a memória ou todo o processador disponível na máquina, prejudicando o funcionamento de todos os outros containers rodando ao lado dele, um problema conhecido informalmente como "vizinho barulhento". Os cgroups existem justamente para evitar esse cenário, impondo limites que o kernel passa a respeitar automaticamente.

## A combinação que torna tudo possível

Namespaces criam a ilusão de uma máquina separada. Cgroups garantem que os recursos físicos sejam divididos de forma justa entre os containers que compartilham a mesma máquina real. Juntos, esses dois mecanismos, presentes no kernel Linux muito antes do Docker existir, são a base técnica que torna possível rodar dezenas de containers isolados numa única máquina, com uma eficiência muito maior do que seria possível usando máquinas virtuais completas, tema aprofundado no arquivo sobre a [relação entre containers e sistemas operacionais](08-containers-e-sistema-operacional.md).

## Fontes

- [What Are Namespaces and cgroups, and How Do They Work?, F5/NGINX](https://blog.nginx.org/blog/what-are-namespaces-cgroups-how-do-they-work)
- [Docker Container Isolation, Dash0](https://www.dash0.com/faq/docker-container-isolation)
- [Container security fundamentals part 2: Isolation & namespaces, Datadog Security Labs](https://securitylabs.datadoghq.com/articles/container-security-fundamentals-part-2/)
