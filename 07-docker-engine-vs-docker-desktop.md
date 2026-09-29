# Docker Engine e Docker Desktop: diferenças na hora de instalar

## Duas formas diferentes de ter o Docker rodando

Depois de entender a arquitetura cliente-servidor do Docker, apresentada no arquivo anterior, falta uma decisão prática: qual pacote instalar. "Docker" nesse contexto pode significar duas coisas bem diferentes, o Docker Engine ou o Docker Desktop, e a escolha certa depende do sistema operacional e do contexto de uso.

## Docker Engine: o núcleo, nativo do Linux

O Docker Engine é o núcleo de código aberto do Docker: o daemon (`dockerd`), o cliente de linha de comando, e a API que conecta os dois, exatamente como apresentado no arquivo sobre a [arquitetura cliente-servidor](06-arquitetura-cliente-servidor-docker-daemon.md). Ele roda nativamente no Linux, sem nenhuma camada extra de virtualização, já que os mecanismos de isolamento usados pelo Docker, namespaces e cgroups, apresentados no arquivo correspondente, já fazem parte do próprio kernel Linux.

A instalação do Docker Engine costuma ser feita via linha de comando, usando o gerenciador de pacotes da distribuição, o mesmo `apt` já apresentado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/04-pacotes-scripts-automacao/01-apt-dpkg-gerenciamento-pacotes.md), sem interface gráfica nem ferramentas visuais adicionais.

## Docker Desktop: a experiência completa, multiplataforma

O Docker Desktop é uma aplicação com interface gráfica, voltada principalmente para desenvolvedores, que inclui o Docker Engine por dentro, mas acrescenta uma camada de conveniência por cima: instalador único, painel visual para gerenciar containers e imagens, controle de recursos (CPU, memória, disco) através de uma interface gráfica, e o Docker Compose já vindo pré-instalado e configurado.

O detalhe mais importante do Docker Desktop é que ele roda nativamente em Windows e macOS, sistemas que não têm o kernel Linux, apresentado no arquivo sobre a [relação entre containers e sistemas operacionais](08-containers-e-sistema-operacional.md), e por isso não conseguem rodar containers Linux diretamente. Para resolver isso, o Docker Desktop sobe, por baixo dos panos, uma máquina virtual leve rodando Linux (usando o WSL2 no Windows, um recurso já apresentado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/06-fundamentos-so-unix-shell/07-ambiente-dev-macos-windows.md)), e é dentro dessa máquina virtual que o daemon de fato roda e os containers de fato existem, mesmo que o cliente pareça estar rodando direto no sistema operacional do usuário.

## Como escolher

Para servidores Linux de produção, onde o objetivo é rodar containers com o menor custo de recursos possível, sem necessidade de interface gráfica, o Docker Engine sozinho é a escolha natural, e costuma ser inclusive a única opção realista. Para uso em notebooks de desenvolvimento, especialmente em Windows ou macOS, o Docker Desktop simplifica bastante a experiência, escondendo a complexidade da máquina virtual por baixo, e entregando ferramentas visuais que facilitam bastante o trabalho do dia a dia de quem está construindo e testando containers localmente.

## Fontes

- [Docker Desktop vs Docker Engine: What's the Difference?, Make Tech Easier](https://maketecheasier.com/docker-desktop-vs-docker-engine/)
- [Understand the Difference Between Docker Engine and Docker Desktop, TheSecMaster](https://medium.com/thesecmaster/understand-the-difference-between-docker-engine-and-docker-desktop-with-thesecmaster-0c2fecec926f)
- [How to Check Your Docker Version: Docker Desktop vs. Docker Engine, Docker Blog](https://www.docker.com/blog/how-to-check-docker-version/)
