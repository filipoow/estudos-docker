# Como o Docker simplificou containers através de imagens versionadas

## O problema que existia antes do formato de imagem do Docker

O arquivo sobre a [evolução dos containers](01-evolucao-chroot-ate-docker.md) mostrou que a tecnologia de isolamento já existia antes do Docker, através do LXC. O que faltava era um jeito padronizado, simples e portável de empacotar uma aplicação junto com tudo que ela precisa para rodar, e de compartilhar esse pacote com outras pessoas ou outras máquinas. Antes do Docker, configurar um ambiente idêntico em máquinas diferentes normalmente exigia scripts de provisionamento longos e frágeis, sujeitos a pequenas diferenças de versão que causavam o clássico problema de "na minha máquina funciona".

## Imagem: o modelo, container: a instância rodando

Uma imagem Docker é um modelo somente leitura, contendo tudo que uma aplicação precisa: o código, as bibliotecas, as dependências e as configurações necessárias. Um container é uma instância em execução dessa imagem, com uma camada adicional de escrita própria por cima, onde ficam as mudanças feitas enquanto aquele container específico está rodando.

Essa distinção é parecida com a diferença entre uma classe e um objeto em programação orientada a objetos: a imagem é a definição, o container é a coisa de fato rodando, e é perfeitamente possível criar vários containers diferentes a partir da mesma imagem, cada um funcionando de forma independente dos demais.

## Camadas: a peça que torna tudo eficiente

Uma imagem Docker não é um bloco único e indivisível, ela é construída em camadas, cada uma representando uma etapa específica de construção, definida por instruções dentro de um Dockerfile (como `RUN`, `COPY` ou `ADD`, já apresentadas no arquivo sobre [Docker e Golang](https://github.com/filipoow/estudos-linux/blob/main/07-processamento-texto-e-automacao/08-docker-golang-processamento-logs.md) do meu outro repositório). Cada camada representa só a diferença (o "diff") em relação à camada anterior.

Esse sistema de camadas traz dois benefícios práticos importantes. O primeiro é velocidade de construção: se só uma parte do Dockerfile mudou, o Docker reaproveita as camadas anteriores já construídas, sem precisar refazer tudo do zero. O segundo é economia de espaço e de banda: camadas idênticas entre imagens diferentes são compartilhadas fisicamente no disco, e ao baixar uma imagem nova, só as camadas que ainda não existem localmente precisam ser transferidas pela rede.

## Versionamento através de hashes e tags

Cada camada de uma imagem recebe um hash único, calculado a partir do seu conteúdo exato. Isso significa que qualquer mudança, por menor que seja, num arquivo de configuração dentro da imagem, gera um hash completamente diferente, garantindo que a mesma imagem usada em testes seja, byte a byte, idêntica à imagem que efetivamente vai para produção.

Além desse identificador técnico, imagens também recebem tags legíveis por humanos, como `minha-app:1.2.0` ou `minha-app:latest`, facilitando referenciar uma versão específica sem precisar lidar diretamente com hashes longos. Esse sistema de versionamento simples, combinado com o modelo de camadas, foi o que tornou possível compartilhar aplicações inteiras, prontas para rodar, através de um simples `docker pull`, algo que antes do Docker exigia documentação extensa e configuração manual repetida em cada máquina nova.

## Fontes

- [Understanding the image layers, Docker Docs](https://docs.docker.com/get-started/docker-concepts/building-images/understanding-image-layers/)
- [What Are Docker Image Layers?, How-To Geek](https://www.howtogeek.com/devops/what-are-docker-image-layers/)
- [Dockerfile Versioning Explained: Strategies, Best Practices, phoenixNAP](https://phoenixnap.com/kb/dockerfile-versioning)
