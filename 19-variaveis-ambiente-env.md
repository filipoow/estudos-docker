# Variáveis de ambiente para configuração dinâmica: a instrução ENV

## Evitando reconstruir a imagem para cada pequeno ajuste

Um Dockerfile como os apresentados até aqui neste bloco de aulas funciona, mas tem uma limitação prática: qualquer configuração escrita diretamente no código, como um endereço de banco de dados ou um nível de log, exigiria reconstruir a imagem inteira toda vez que precisasse mudar. A instrução `ENV`, combinada com o conceito de variáveis de ambiente já apresentado em detalhe no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/08-variaveis-condicionais-scripts-avancados/01-variaveis-ambiente-locais.md), resolve exatamente esse problema.

## Definindo variáveis com ENV

```dockerfile
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
```

Também é possível definir várias variáveis numa única instrução, o que gera menos camadas na imagem, retomando o conceito já apresentado no arquivo sobre [imagens versionadas](03-imagens-versionadas.md):

```dockerfile
ENV FLASK_APP=app.py FLASK_RUN_HOST=0.0.0.0 PYTHONUNBUFFERED=1
```

Essas variáveis ficam disponíveis para qualquer processo rodando dentro do container, exatamente como qualquer outra variável de ambiente do sistema.

## Sobrescrevendo o valor em tempo de execução

O grande poder do `ENV` está justamente em poder ser sobrescrito sem tocar no Dockerfile nem reconstruir a imagem, usando a opção `-e` do `docker run`:

```
docker run -e FLASK_RUN_HOST=127.0.0.1 minha-imagem
```

Esse comando substitui, só para esse container específico, o valor que havia sido definido dentro do Dockerfile, sem afetar a imagem em si nem outros containers criados a partir dela. É essa flexibilidade que permite usar exatamente a mesma imagem em ambientes diferentes, desenvolvimento, teste e produção, mudando só as variáveis relevantes para cada contexto.

Para várias variáveis de uma vez, existe ainda a opção de um arquivo externo:

```
docker run --env-file=./producao.env minha-imagem
```

## A diferença entre ENV e ARG

Vale uma distinção importante que costuma confundir: existe também a instrução `ARG`, usada para valores disponíveis só durante o processo de construção da imagem (`docker build`), sem persistir depois, quando o container de fato roda. Um padrão comum é combinar as duas, usando um `ARG` para receber um valor no momento da construção, e repassando esse valor para dentro de uma variável `ENV` que vai persistir no container final:

```dockerfile
ARG VERSAO_APP=1.0
ENV VERSAO_APP=${VERSAO_APP}
```

## Por que isso importa para configuração dinâmica

Combinando `ENV` no Dockerfile (para um valor padrão sensato) com `-e` no `docker run` (para sobrescrever quando necessário), uma mesma imagem, construída uma única vez, pode ser reaproveitada em contextos completamente diferentes sem nenhuma alteração no seu código interno, só ajustando a configuração externa que é entregue a ela no momento de iniciar cada container específico.

## Fontes

- [How to Set Docker Environment Variables, phoenixNAP](https://phoenixnap.com/kb/docker-environment-variables)
- [Docker ARG, ENV and .env, a Complete Guide, vsupalov.com](https://vsupalov.com/docker-arg-env-variable-guide/)
- [Build variables, Docker Docs](https://docs.docker.com/build/building/variables/)
