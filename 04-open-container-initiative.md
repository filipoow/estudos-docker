# A contribuição da Open Container Initiative para padronização

## Quando um único formato de mercado vira um problema

Depois que o Docker popularizou containers, apresentado no arquivo sobre [imagens versionadas](03-imagens-versionadas.md), o formato de imagem e a forma de executar containers criados por ele se tornaram, na prática, o padrão de fato do mercado, mesmo sem ser um padrão formal, aberto e independente de uma empresa específica. Isso criava um risco real: outras ferramentas que quisessem interoperar com esse ecossistema, criando, rodando ou distribuindo containers, dependiam de decisões internas de uma única empresa, sem garantia de estabilidade a longo prazo.

## A criação da OCI

A Open Container Initiative (OCI) foi fundada em 2015 como uma estrutura de governança aberta, com o objetivo explícito de criar padrões de indústria para formatos e runtimes de container, independentes de qualquer fornecedor específico. O próprio Docker participou ativamente da criação da OCI, doando parte de seu código e de suas especificações como ponto de partida para o padrão aberto que viria a seguir.

## As três especificações centrais

A OCI mantém três especificações principais, cada uma cobrindo uma parte diferente do ciclo de vida de um container:

- **Image Specification (image-spec)**: define o formato de uma imagem de container, incluindo um manifesto, uma lista opcional de índices de imagem, um conjunto de camadas de sistema de arquivos (o mesmo conceito de camadas já apresentado no arquivo sobre [imagens versionadas](03-imagens-versionadas.md)) e um arquivo de configuração.
- **Runtime Specification (runtime-spec)**: define como executar, de fato, um "pacote de sistema de arquivos" já desempacotado em disco. O `runc`, hoje amplamente usado como motor de execução de baixo nível por várias ferramentas de container, é a implementação de referência dessa especificação.
- **Distribution Specification (distribution-spec)**: define um protocolo de API para padronizar como imagens são distribuídas e armazenadas em registros (registries), o tipo de serviço usado para hospedar e baixar imagens.

## O que isso muda, na prática

Graças à OCI, uma imagem construída com o Docker consegue rodar em outras ferramentas compatíveis, como o Podman, sem precisar de nenhuma conversão. Da mesma forma, uma imagem guardada em qualquer registro compatível com a especificação de distribuição pode ser baixada por qualquer ferramenta que também siga esse mesmo padrão, e a execução de um container se comporta de forma consistente, não importa qual runtime específico esteja de fato rodando por baixo.

Essa padronização é o que permite, por exemplo, que uma plataforma de orquestração como o Kubernetes, apresentado no [próximo arquivo](05-kubernetes-orquestracao.md), funcione com diferentes runtimes de container por baixo dos panos, sem estar amarrada especificamente ao Docker, mesmo tendo o Docker como o responsável histórico por popularizar essa tecnologia inteira.

## Fontes

- [Open Container Initiative, Wikipédia](https://en.wikipedia.org/wiki/Open_Container_Initiative)
- [Open Container Initiative specifications reach 1.0, Red Hat](https://www.redhat.com/en/blog/open-container-initiative-specifications-reach-10)
- [Demystifying the Open Container Initiative (OCI) Specifications, Docker Blog](https://www.docker.com/blog/demystifying-open-container-initiative-oci-specifications/)
