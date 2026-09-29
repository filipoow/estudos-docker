# A anatomia de um Dockerfile: FROM, RUN, COPY, ADD e WORKDIR

## A receita por trás de uma imagem

Todo esse bloco de aulas até aqui falou sobre containers já criados, prontos para rodar. Mas de onde vem uma imagem, apresentada no arquivo sobre [imagens versionadas](03-imagens-versionadas.md)? Ela vem de um Dockerfile, um arquivo de texto simples contendo uma sequência de instruções que descrevem, passo a passo, como construir aquela imagem, exatamente como já apareceu de forma resumida no arquivo sobre [Docker e Golang](https://github.com/filipoow/estudos-linux/blob/main/07-processamento-texto-e-automacao/08-docker-golang-processamento-logs.md) do meu outro repositório de estudos de Linux. Vale agora entender cada uma das instruções mais fundamentais em profundidade.

## `FROM`: o ponto de partida obrigatório

```dockerfile
FROM node:20
```

Todo Dockerfile precisa começar com um `FROM`, definindo a imagem base sobre a qual tudo o mais será construído. É a fundação: se a imagem base já vem com Node.js instalado, por exemplo, não é preciso instalar isso manualmente depois, ela já parte desse ponto.

## `RUN`: executando comandos durante a construção

```dockerfile
RUN apt-get update && apt-get install -y curl
```

O `RUN` executa um comando durante o processo de construção da imagem, criando uma nova camada por cima da anterior, o mesmo conceito de camadas já apresentado no arquivo sobre [imagens versionadas](03-imagens-versionadas.md). É usado tipicamente para instalar pacotes, compilar código, ou qualquer preparação que precise acontecer antes da imagem estar pronta.

## `COPY` e `ADD`: trazendo arquivos para dentro da imagem

```dockerfile
COPY ./app /app
```

O `COPY` copia arquivos e pastas do sistema de arquivos local (onde o `docker build` está sendo executado) para dentro da imagem. O `ADD` faz basicamente a mesma coisa, mas com poderes extras: além de copiar arquivos locais, ele também consegue baixar arquivos de uma URL remota, e descompactar automaticamente arquivos compactados reconhecidos, como `.tar.gz`.

Apesar desses recursos extras parecerem uma vantagem, a recomendação amplamente seguida é preferir `COPY` na maioria dos casos, justamente pela previsibilidade: `COPY` faz exatamente uma coisa, copiar arquivos locais, sem introduzir riscos de segurança ou comportamentos inesperados vindos de descompactação automática ou downloads remotos escondidos dentro de uma instrução aparentemente simples. `ADD` fica reservado para os casos específicos em que suas capacidades extras são realmente necessárias.

## `WORKDIR`: definindo a pasta de trabalho

```dockerfile
WORKDIR /app
```

O `WORKDIR` define a pasta de trabalho para todas as instruções seguintes no Dockerfile, como `RUN`, `CMD`, `COPY` e `ADD`. A diferença fundamental em relação a simplesmente rodar um `cd` dentro de um `RUN` é que o `WORKDIR` persiste entre instruções diferentes, enquanto um `cd` dentro de um único `RUN` só vale para aquele comando específico, sendo esquecido logo em seguida. Usar `WORKDIR` de forma consistente evita ter que repetir caminhos completos em cada instrução do Dockerfile.

## Um exemplo juntando as quatro instruções

```dockerfile
FROM node:20
WORKDIR /app
COPY . .
RUN npm install
```

Essa sequência parte de uma imagem já com Node.js, define `/app` como pasta de trabalho, copia todo o conteúdo do projeto para dentro dela, e instala as dependências. É, em essência, o esqueleto mínimo de praticamente qualquer Dockerfile voltado a aplicações Node.js.

## Fontes

- [Dockerfile reference, Docker Docs](https://docs.docker.com/reference/dockerfile/)
- [Docker Best Practices: Understanding the Differences Between ADD and COPY, Docker Blog](https://www.docker.com/blog/docker-best-practices-understanding-the-differences-between-add-and-copy-instructions-in-dockerfiles/)
- [How to Use the WORKDIR Instruction in Dockerfiles, oneuptime](https://oneuptime.com/blog/post/2026-02-08-how-to-use-the-workdir-instruction-in-dockerfiles/view)
