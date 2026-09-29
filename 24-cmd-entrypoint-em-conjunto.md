# Como CMD e ENTRYPOINT podem interagir e ser usados em conjunto

## Combinando o melhor dos dois mundos

O arquivo sobre [CMD e ENTRYPOINT](18-cmd-vs-entrypoint.md) apresentou as duas instruções separadamente: `CMD`, fácil de substituir por completo, e `ENTRYPOINT`, fixo e sempre executado. Existe uma terceira forma de usá-las, combinando as duas na mesma imagem, que costuma ser a abordagem mais flexível e mais profissional na prática.

## O padrão: ENTRYPOINT fixo, CMD como argumento padrão

Quando as duas instruções aparecem juntas no mesmo Dockerfile, o Docker trata o valor definido em `CMD` como os argumentos padrão a serem entregues ao comando fixo definido em `ENTRYPOINT`, e não mais como um comando independente e completo por conta própria.

```dockerfile
ENTRYPOINT ["python", "app.py"]
CMD ["--modo=producao"]
```

Rodando esse container sem nenhum argumento extra, o resultado é `python app.py --modo=producao`, juntando o `ENTRYPOINT` com o `CMD` padrão. Mas rodando com um argumento diferente:

```
docker run minha-imagem --modo=teste
```

O resultado passa a ser `python app.py --modo=teste`, já que o argumento passado na linha de comando substitui só o `CMD`, mantendo o `ENTRYPOINT` intacto, exatamente o comportamento apresentado no arquivo anterior sobre `ENTRYPOINT`.

## Um exemplo mais próximo do dia a dia

```dockerfile
ENTRYPOINT ["echo", "Ola"]
CMD ["Mundo"]
```

Sem argumentos extras, esse container imprime "Ola Mundo". Rodando `docker run minha-imagem Filipe`, o resultado passa a ser "Ola Filipe", já que "Filipe" substitui o `CMD` padrão ("Mundo"), enquanto o `ENTRYPOINT` ("echo Ola") continua fixo, sempre presente.

## Por que essa combinação é considerada a mais profissional

Esse padrão resolve exatamente o problema que nenhuma das duas instruções sozinha resolve bem: um executável fixo e garantido (o `ENTRYPOINT`, que impede alguém de acidentalmente substituir o programa principal por outro comando qualquer sem querer), combinado com parâmetros configuráveis e flexíveis (o `CMD`, funcionando como um valor padrão sensato, mas fácil de ajustar conforme a necessidade de cada execução específica).

Vale reforçar uma recomendação já apresentada de passagem no arquivo anterior: ao combinar as duas instruções, ambas deveriam ser escritas na forma de lista (chamada forma exec, com colchetes e cada palavra entre aspas), como nos exemplos deste arquivo, e não como uma string simples de texto corrido. A forma de lista evita ambiguidades na interpretação de espaços e argumentos, e é o que garante que o mecanismo de substituição de argumentos, apresentado aqui, funcione de forma previsível.

## Fontes

- [Docker ENTRYPOINT and CMD: Differences & Examples, Collabnix](https://collabnix.com/docker-entrypoint-and-cmd-differences-examples/)
- [Docker Best Practices: Choosing Between RUN, CMD, and ENTRYPOINT, Docker Blog](https://www.docker.com/blog/docker-best-practices-choosing-between-run-cmd-and-entrypoint/)
- [Docker CMD and ENTRYPOINT Differences, Devtron](https://devtron.ai/blog/cmd-and-entrypoint-differences/)
