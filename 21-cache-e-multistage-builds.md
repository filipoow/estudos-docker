# Otimização do build: cache de camadas e múltiplos estágios

## Construir uma imagem não precisa ser lento

Um Dockerfile mal organizado pode levar minutos para construir, mesmo quando só uma linha de código da aplicação mudou. A boa notícia é que o Docker já vem com um mecanismo de cache poderoso embutido, e entender como ele funciona é o primeiro passo para escrever Dockerfiles rápidos de construir.

## Como o cache de camadas funciona

Cada instrução de um Dockerfile, já apresentadas nos arquivos sobre [instruções básicas](17-anatomia-dockerfile-instrucoes-basicas.md) e [variáveis de ambiente](19-variaveis-ambiente-env.md), gera uma nova camada, retomando o conceito já apresentado no arquivo sobre [imagens versionadas](03-imagens-versionadas.md). Ao construir uma imagem, o Docker verifica se já existe, no cache, uma camada idêntica à que seria gerada por aquela instrução específica. Se existir, ele reaproveita essa camada em cache, sem executar o comando de novo. Se não existir, ele executa a instrução, e, a partir desse ponto, todas as camadas seguintes também precisam ser reconstruídas, mesmo que elas mesmas não tenham mudado, já que cada camada depende do estado deixado pela anterior.

Essa última regra é a chave para otimizar um Dockerfile: instruções que mudam raramente deveriam vir no início do arquivo, e instruções que mudam com frequência deveriam vir por último.

## Um exemplo prático de otimização

```dockerfile
FROM node:20
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
```

Repare que o `package.json` é copiado, e as dependências são instaladas, antes de copiar o resto do código da aplicação. Isso significa que, enquanto as dependências do projeto não mudarem, o Docker reaproveita a camada já construída do `npm install`, mesmo que o código da aplicação (que muda com muito mais frequência) tenha sido alterado. Se a ordem fosse invertida, copiando tudo de uma vez antes de instalar dependências, qualquer alteração de código, por menor que fosse, forçaria reinstalar todas as dependências do zero a cada build.

## Múltiplos estágios: separando construção de execução

Um segundo mecanismo de otimização, ainda mais poderoso, são os builds em múltiplos estágios (multi-stage builds). A ideia é usar uma imagem completa, com todas as ferramentas de compilação, só para construir a aplicação, e depois copiar apenas o resultado final dessa construção para dentro de uma imagem final bem mais enxuta, sem carregar as ferramentas de build que não são mais necessárias em produção.

```dockerfile
FROM golang:1.22 AS build
WORKDIR /app
COPY . .
RUN go build -o servidor .

FROM debian:bookworm-slim
COPY --from=build /app/servidor /usr/local/bin/servidor
CMD ["servidor"]
```

Esse exemplo, já usado de forma resumida no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/07-processamento-texto-e-automacao/08-docker-golang-processamento-logs.md), usa um primeiro estágio (`AS build`) com a imagem completa do Go para compilar o binário, e um segundo estágio, baseado numa imagem bem mais leve, que copia só o binário já compilado através de `COPY --from=build`, descartando tudo o mais que fazia parte do primeiro estágio.

## Os benefícios somados

Builds em múltiplos estágios reduzem drasticamente o tamanho final da imagem (menos coisa para transferir e armazenar), melhoram a segurança (menos ferramentas desnecessárias presentes na imagem final, cada uma delas um risco potencial a menos), e, combinados com uma boa ordem de instruções para aproveitar o cache, tornam o ciclo de construir, testar e publicar uma imagem muito mais rápido do que seria com um Dockerfile de um único estágio, escrito sem essa preocupação.

## Fontes

- [Understanding Multi-Stage Docker Builds, Blacksmith](https://www.blacksmith.sh/blog/understanding-multi-stage-docker-builds)
- [How to Optimize Your Docker Build Cache & Cut Your CI/CD Pipeline, freeCodeCamp](https://www.freecodecamp.org/news/how-to-optimize-your-docker-build-cache/)
- [How to Implement Docker Layer Caching Strategies, oneuptime](https://oneuptime.com/blog/post/2026-01-30-how-to-implement-docker-layer-caching-strategies/view)
