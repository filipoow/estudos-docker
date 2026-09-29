# A relação entre containers e a estrutura de sistemas operacionais

## Fechando o círculo: containers dependem diretamente do kernel

Cada arquivo deste repositório apontou, de um jeito ou de outro, para a mesma ideia central: containers não são uma tecnologia isolada e autocontida, eles são construídos diretamente sobre a estrutura de um sistema operacional real, especialmente sobre o kernel Linux. Vale fechar esse raciocínio de forma explícita, comparando containers com sua alternativa mais conhecida, as máquinas virtuais.

## Máquina virtual: um sistema operacional inteiro, duplicado

Uma máquina virtual é construída sobre uma camada de software chamada hipervisor, que permite que cada máquina virtual rode seu próprio sistema operacional completo e independente, com seu próprio kernel, mesmo estando fisicamente dentro do mesmo servidor que outras máquinas virtuais. Isso garante um isolamento muito forte, já que cada máquina virtual nem sequer compartilha o kernel com as demais, mas tem um custo real: cada máquina virtual precisa inicializar um sistema operacional inteiro, consumindo memória e processador só para manter esse sistema rodando, independente de qualquer aplicação de fato útil que esteja sendo executada dentro dela.

## Container: compartilhando o mesmo kernel

Um container, ao contrário, não tem seu próprio kernel. Ele compartilha diretamente o kernel do sistema operacional hospedeiro, usando os mecanismos já apresentados nos arquivos sobre [namespaces e cgroups](02-namespaces-e-cgroups.md) para criar a ilusão de isolamento, sem precisar duplicar o sistema operacional inteiro. Um container empacota a aplicação junto com suas bibliotecas e dependências específicas, mas não com um kernel próprio, ele sempre depende do kernel da máquina onde está rodando.

Essa diferença de arquitetura explica praticamente todas as vantagens práticas dos containers sobre máquinas virtuais: um container inicia em milissegundos, já que não precisa inicializar um sistema operacional do zero, enquanto uma máquina virtual costuma levar segundos ou até minutos para estar pronta. Pela mesma razão, é possível rodar de cinco a dez vezes mais containers do que máquinas virtuais no mesmo hardware físico, já que containers não pagam o custo repetido de manter vários kernels completos rodando ao mesmo tempo.

## A implicação que isso traz: containers Linux exigem um kernel Linux

Essa dependência direta do kernel também explica por que, como já mencionado no arquivo sobre [Docker Engine e Docker Desktop](07-docker-engine-vs-docker-desktop.md), containers Linux não rodam nativamente em Windows ou macOS: esses sistemas não têm o kernel Linux necessário para que os mecanismos de namespaces e cgroups funcionem. A solução prática de mercado, uma máquina virtual leve rodando Linux por baixo do Docker Desktop, é, de certa forma, uma ironia interessante: para rodar uma tecnologia criada justamente para evitar o peso de máquinas virtuais completas, sistemas fora do Linux acabam precisando de uma máquina virtual de qualquer forma, só que escondida da vista do usuário.

## Uma contrapartida a considerar: segurança

Vale registrar também o lado menos favorável dessa arquitetura compartilhada: como todos os containers de uma máquina dividem o mesmo kernel, uma falha de segurança grave descoberta nesse kernel compartilhado pode, em teoria, comprometer todos os containers rodando ali, algo que não acontece da mesma forma com máquinas virtuais, isoladas por hardware emulado próprio. Isso não torna containers inseguros no uso comum, mas é uma diferença real de modelo de ameaça que vale ter em mente, especialmente em ambientes que hospedam código de origens não totalmente confiáveis lado a lado.

## Fontes

- [Containerization vs. virtualization: Key differences explained, Wiz](https://www.wiz.io/academy/container-security/containerization-vs-virtualization)
- [Containers vs Virtual Machines: Key Differences Explained, Civo](https://www.civo.com/academy/before-kubernetes/containers-vs-virtual-machines)
- [Containers vs virtual machines: Key differences, CircleCI](https://circleci.com/blog/containers-vs-virtual-machines/)
