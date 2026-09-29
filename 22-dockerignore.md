# O arquivo .dockerignore: excluindo o que não deveria entrar na imagem

## O contexto de build inclui mais do que parece

Ao rodar `docker build`, o Docker não olha só para o Dockerfile, ele envia toda a pasta onde o comando foi executado (o chamado contexto de build) para o daemon, apresentado no arquivo sobre a [arquitetura cliente-servidor do Docker](06-arquitetura-cliente-servidor-docker-daemon.md), antes mesmo de começar a processar qualquer instrução. Sem nenhum filtro, isso pode incluir uma quantidade enorme de arquivos que não deveriam fazer parte da imagem final, nem precisam ser transferidos durante o build.

## O que é o .dockerignore

O `.dockerignore` é um arquivo de texto, guardado na mesma pasta do Dockerfile, que segue um espírito bem parecido com o `.gitignore` já usado no controle de versão com Git, apresentado no [meu outro repositório de estudos de Git e GitHub](https://github.com/filipoow/estudos-git-github). Cada linha define um padrão de arquivo ou pasta a ser ignorado durante o build.

```
node_modules
*.log
.git
.env
```

Esse exemplo ignora a pasta de dependências do Node.js, arquivos de log, a pasta de controle de versão do Git, e um arquivo comum de variáveis de ambiente locais.

## Três razões concretas para usar

**Reduzir o tamanho e acelerar a transferência**: quanto menor o contexto de build enviado ao daemon, mais rápido o processo começa, especialmente relevante em pastas de projeto com dependências pesadas, como a clássica `node_modules`.

**Acelerar o cache de camadas**: retomando o conceito apresentado no arquivo sobre [cache e múltiplos estágios](21-cache-e-multistage-builds.md), uma instrução `COPY . .` considera qualquer mudança dentro da pasta copiada como motivo para invalidar o cache daquela camada. Arquivos irrelevantes, como logs gerados durante o desenvolvimento, mudando constantemente, invalidariam o cache sem necessidade nenhuma, mesmo sem afetar de fato a aplicação.

**Segurança**: talvez a razão mais importante de todas. Arquivos sensíveis, como chaves privadas, credenciais ou arquivos `.env` contendo segredos, nunca deveriam acabar dentro de uma imagem Docker, já que qualquer pessoa com acesso a essa imagem conseguiria potencialmente extrair esse conteúdo depois. O `.dockerignore` garante que esses arquivos fiquem de fora por padrão, mesmo que alguém esqueça de removê-los manualmente antes de rodar o build.

## Sintaxe e curingas

Assim como em outros arquivos de padrão já apresentados neste conjunto de repositórios, o `.dockerignore` aceita curingas, como `*` para qualquer sequência de caracteres, retomando o conceito de globbing já apresentado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/09-funcoes-arrays-e-tratamento-de-erros/04-for-listas-numeros-globbing.md), e o símbolo `!` para negar um padrão, reincluindo especificamente um arquivo que, de outra forma, cairia dentro de uma regra mais ampla de exclusão.

## Um hábito a criar desde o início do projeto

Diferente de otimizações que podem ser adicionadas depois, sem grande prejuízo, o `.dockerignore` vale a pena configurar desde o primeiro Dockerfile de um projeto, evitando desde já builds lentos e, principalmente, evitando o risco real de vazar informação sensível dentro de uma imagem que, mais cedo ou mais tarde, pode acabar publicada num registro, tema aprofundado no [próximo arquivo](23-publicando-imagens-registries.md).

## Fontes

- [Docker .dockerignore Explained, GoLinuxCloud](https://www.golinuxcloud.com/dockerignore-file/)
- [Do Not Ignore .dockerignore (it's Expensive And Potentially Dangerous), Octopus Deploy](https://octopus.com/blog/not-ignore-dockerignore-2)
- [What are .dockerignore files, and why you should use them?, TechRepublic](https://www.techrepublic.com/article/what-is-a-dockerignore-file-and-why-you-should-be-using-them/)
