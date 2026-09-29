# A evolução dos containers: do chroot até o Docker

## O Docker não inventou a ideia de container

Uma impressão comum, mas equivocada, é achar que o Docker criou o conceito de isolar aplicações dentro de containers. Na realidade, o Docker chegou relativamente tarde nessa história, em 2013, depois de décadas de tentativas anteriores tentando resolver o mesmo problema: como rodar programas isolados uns dos outros, sem o custo de uma máquina virtual completa. O mérito do Docker não foi inventar o isolamento, foi tornar essa tecnologia, já existente, acessível e prática para qualquer desenvolvedor usar no dia a dia.

## 1979: chroot, o primeiro passo

A raiz de tudo remonta a 1979, com o comando `chroot`, ainda presente em sistemas Unix e Linux até hoje. O `chroot` muda a pasta raiz que um processo (e os processos filhos dele) enxerga, criando uma espécie de "sistema de arquivos dentro do sistema de arquivos", isolado do restante da máquina. É um isolamento bem limitado, o `chroot` não isola rede, processos ou uso de recursos, só o sistema de arquivos visível, mas foi a primeira peça de um quebra-cabeça que levaria décadas para se completar.

## 2000: FreeBSD Jails, isolamento mais completo

Em 2000, o FreeBSD, outro sistema derivado do Unix já apresentado no estudo de [história e influência do Unix](https://github.com/filipoow/estudos-linux/blob/main/06-fundamentos-so-unix-shell/02-historia-influencia-unix.md) do meu outro repositório, introduziu o conceito de jails (jaulas), indo além do `chroot`: cada jail passou a ter sua própria interface de rede e endereço IP, um isolamento bem mais próximo do que se entende hoje por container.

## 2008: LXC, containers nativos no Linux

O LXC (Linux Containers), lançado em 2008, foi construído diretamente sobre dois mecanismos do próprio kernel Linux: namespaces e cgroups, aprofundados no [próximo arquivo](02-namespaces-e-cgroups.md). Diferente das soluções anteriores, o LXC já oferecia isolamento de processos, rede e uso de recursos de forma nativa, sem precisar de camadas extras, e contou com contribuições importantes de engenheiros do Google.

## 2013: Docker, a virada de chave

O Docker nasceu dentro da dotCloud, uma empresa de plataforma como serviço, criado por Solomon Hykes, e foi liberado como projeto de código aberto em 2013. Nas primeiras versões, o Docker usava o próprio LXC como motor de execução por baixo dos panos. Menos de um ano depois, essa dependência foi substituída por uma ferramenta própria, o `libcontainer`, escrita na linguagem Go.

O diferencial real do Docker não estava na tecnologia de isolamento em si, que já existia havia anos através do LXC, mas na experiência em torno dela: um formato de imagem simples de construir e versionar, apresentado no arquivo sobre [imagens versionadas](03-imagens-versionadas.md), e uma linha de comando amigável, que tornou possível para qualquer desenvolvedor empacotar e rodar uma aplicação isolada sem precisar entender profundamente namespaces e cgroups por baixo.

## Fontes

- [Evolution of Docker from Linux Containers, Baeldung on Linux](https://www.baeldung.com/linux/docker-containers-evolution)
- [A Brief History of Containers: From the 1970s Till Now, Aqua Security](https://www.aquasec.com/blog/a-brief-history-of-containers-from-1970s-chroot-to-docker-2016/)
- [The History of Container Technology, Pluralsight](https://www.pluralsight.com/resources/blog/cloud/history-of-container-technology)
