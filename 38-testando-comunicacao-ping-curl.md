# Testando a comunicação entre containers com ping e curl

## Não basta criar a rede, é preciso confirmar que funciona

Depois de montar uma rede e conectar containers a ela, como apresentado nos arquivos sobre [criação de redes](36-docker-network-create-inspect-connect.md) e [execução de containers em redes específicas](37-executando-containers-em-redes-especificas.md), vale confirmar na prática que a comunicação funciona como esperado. Para isso, as mesmas ferramentas de diagnóstico de rede já apresentadas no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/05-rede-usuarios-seguranca/06-ping-nslookup-diagnostico.md) se aplicam, só que executadas de dentro de um container.

## Preparando um cenário de teste

Um cenário simples e clássico usa duas coisas: um servidor web rodando em um container, e um segundo container, só para fazer as vezes de cliente, de onde os testes serão disparados.

```
docker network create teste-rede

docker run -d --name servidor --network teste-rede nginx

docker run -it --name cliente --network teste-rede alpine sh
```

O terceiro comando abre um terminal interativo dentro de um container Alpine, como apresentado no arquivo sobre [modo interativo](13-modo-interativo-e-background.md), já conectado à mesma rede do servidor.

## Testando com ping

De dentro do container cliente:

```
ping servidor
```

O `ping`, apresentado em detalhe no outro repositório, envia pacotes de teste e mede se há resposta. Repare que o destino é o nome do container, `servidor`, e não um endereço IP: isso só funciona porque a rede foi criada pelo usuário, com DNS embutido, como explicado no arquivo sobre [redes customizadas e DNS interno](35-redes-customizadas-dns-interno.md). Se o mesmo teste fosse feito na rede bridge padrão, o nome não seria resolvido, e só o IP funcionaria.

## Testando com curl

O `ping` confirma que existe conectividade básica, mas não que o serviço em si está respondendo. Para isso, o `curl` faz uma requisição HTTP de verdade:

```
curl http://servidor
```

Como o container `servidor` roda o Nginx, a resposta esperada é o HTML da página padrão do Nginx. Isso confirma, de uma vez, três coisas: que a resolução de nomes funcionou, que a rede entre os dois containers está funcionando, e que o serviço está de fato escutando e respondendo na porta esperada.

## Ferramentas que podem não estar instaladas

Imagens enxutas como o Alpine nem sempre trazem todas as ferramentas de diagnóstico instaladas por padrão, o que é coerente com a ideia de manter imagens pequenas, como apresentado no arquivo sobre [cache e múltiplos estágios](21-cache-e-multistage-builds.md). Quando uma ferramenta estiver faltando, é possível instalá-la temporariamente dentro do container, para o teste, ou usar uma imagem de diagnóstico dedicada, que já vem com um conjunto amplo de ferramentas de rede, como a `nicolaka/netshoot`, conectando-a à mesma rede só durante a investigação.

## Isolamento na prática

Um teste complementar e igualmente valioso é verificar o isolamento: criar um terceiro container numa rede diferente e confirmar que ele não consegue alcançar o `servidor`. Se a comunicação falhar como esperado, isso confirma que as redes estão isoladas entre si, exatamente como apresentado no arquivo sobre a [importância das Docker Networks](33-docker-networks-importancia-isolamento.md).

## Fontes

- [How to test communication between containers on the same custom network, LabEx](https://labex.io/tutorials/docker-how-to-test-communication-between-containers-on-the-same-custom-network-411612)
- [Communicating With Docker Containers on the Same Machine, Baeldung on Ops](https://www.baeldung.com/ops/docker-communicating-with-containers-on-same-machine)
- [How to Debug Docker Network Connectivity Issues, oneuptime](https://oneuptime.com/blog/post/2026-01-22-debug-docker-network-connectivity/view)
