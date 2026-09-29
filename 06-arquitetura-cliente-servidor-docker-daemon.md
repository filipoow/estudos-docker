# A arquitetura cliente-servidor do Docker e o Docker Daemon

## O comando que você digita não faz o trabalho pesado sozinho

Quando alguém digita `docker run` no terminal, é fácil imaginar que esse único comando já contém toda a lógica de criar e rodar um container. Na realidade, o Docker é dividido em duas partes distintas, que conversam entre si através de uma arquitetura cliente-servidor: o cliente Docker, que só interpreta e envia comandos, e o Docker Daemon (`dockerd`), o processo que de fato faz o trabalho pesado.

## O cliente Docker

O cliente Docker (o comando `docker`) é a forma mais comum de interagir com o Docker no dia a dia. Quando um comando como `docker run` é digitado, o cliente não cria nenhum container sozinho, ele apenas traduz esse comando numa requisição, e envia essa requisição para o daemon, esperando pela resposta.

## O Docker Daemon (dockerd)

O `dockerd` é um processo que roda em segundo plano no sistema hospedeiro, ouvindo requisições da API do Docker, e gerenciando de fato os objetos do Docker: imagens, containers, redes e volumes. É o daemon quem efetivamente aplica os conceitos já apresentados neste repositório, como namespaces e cgroups, para criar e isolar um container novo, ou quem monta as camadas de uma imagem, apresentadas no arquivo sobre [imagens versionadas](03-imagens-versionadas.md).

## Como cliente e daemon se comunicam

A comunicação entre cliente e daemon acontece através de uma API REST, seja por um socket Unix local (o caso mais comum, quando os dois rodam na mesma máquina), seja por uma interface de rede, o que também torna possível conectar um cliente Docker local a um daemon rodando remotamente, numa máquina completamente diferente. Essa separação entre "quem pede" e "quem executa" é o que permite, por exemplo, gerenciar containers rodando num servidor remoto sem precisar abrir uma conexão de terminal direta naquela máquina, bastando apontar o cliente Docker local para o endereço certo.

## Por que esse desenho importa

Essa arquitetura cliente-servidor não é um detalhe técnico irrelevante, ela é o que possibilita cenários como ferramentas gráficas de terceiros controlando o Docker sem reimplementar toda a lógica de isolamento por conta própria, ou pipelines de integração contínua acionando a criação de containers num servidor remoto de build. Também explica por que, em alguns sistemas, é preciso iniciar (ou verificar se já está rodando) o serviço do `dockerd` antes de qualquer comando `docker` funcionar, exatamente como acontece com qualquer outro serviço gerenciado pelo systemd, tema já aprofundado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/10-devops-monitoramento-e-processos-longos/07-systemd-servicos-personalizados.md).

## Fontes

- [What is Docker?, Docker Docs](https://docs.docker.com/get-started/docker-overview/)
- [Docker Architecture Explained: Client, Daemon & Registry, KodeKloud](https://kodekloud.com/blog/docker-architecture/)
- [Architecture of Docker, GeeksforGeeks](https://www.geeksforgeeks.org/devops/architecture-of-docker/)
