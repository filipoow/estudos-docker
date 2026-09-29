# A diferença entre CMD e ENTRYPOINT na execução de containers

## Duas formas de definir o que um container faz ao iniciar

Depois de montar a estrutura de uma imagem com as instruções apresentadas no [arquivo anterior](17-anatomia-dockerfile-instrucoes-basicas.md), falta definir o que, exatamente, deve acontecer quando um container é iniciado a partir dela. Existem duas instruções para isso, `CMD` e `ENTRYPOINT`, que parecem fazer a mesma coisa à primeira vista, mas se comportam de forma bem diferente quando alguém tenta sobrescrever esse comportamento padrão.

## `CMD`: um padrão fácil de substituir

```dockerfile
CMD ["python", "app.py"]
```

O `CMD` define o comando padrão a ser executado quando o container inicia, mas esse padrão é só isso, um padrão. Se alguém rodar o container passando um comando diferente na linha de comando, esse comando substitui completamente o que estava definido em `CMD`.

```
docker run minha-imagem
```

Roda `python app.py`, como definido em `CMD`.

```
docker run minha-imagem python outro_script.py
```

Ignora completamente o `CMD` do Dockerfile, rodando `python outro_script.py` no lugar.

## `ENTRYPOINT`: o comando principal, que sempre executa

```dockerfile
ENTRYPOINT ["python", "app.py"]
```

O `ENTRYPOINT` define o comando principal do container, um comando que sempre vai rodar, não importa o que seja passado depois do nome da imagem no `docker run`. Qualquer coisa passada ali não substitui o `ENTRYPOINT`, ela é adicionada como argumento extra a ele.

```
docker run minha-imagem --debug
```

Com o `ENTRYPOINT` do exemplo acima, esse comando roda `python app.py --debug`, já que `--debug` é entregue como argumento adicional ao `ENTRYPOINT`, em vez de substituí-lo.

Para de fato sobrescrever um `ENTRYPOINT`, é preciso usar explicitamente a opção `--entrypoint` do `docker run`, um passo deliberado a mais, bem diferente da facilidade de sobrescrever um `CMD` simplesmente passando outro comando.

## Escolhendo entre os dois

Use `CMD` sozinho quando o objetivo é oferecer um comportamento padrão flexível, fácil de trocar por completo conforme a necessidade de quem está rodando o container. Use `ENTRYPOINT` quando o container deveria sempre executar um comando específico e fixo, sem exceções, como um executável que representa a própria razão de existir daquele container. Vale adiantar que existe ainda uma terceira opção, combinando os dois ao mesmo tempo, que aproveita o melhor de cada abordagem, apresentada no arquivo sobre [CMD e ENTRYPOINT em conjunto](24-cmd-entrypoint-em-conjunto.md).

## Fontes

- [Docker ENTRYPOINT vs. CMD: Differences & Examples, Spacelift](https://spacelift.io/blog/docker-entrypoint-vs-cmd)
- [Docker CMD vs. ENTRYPOINT: What's the Difference and How to Choose, BMC](https://www.bmc.com/blogs/docker-cmd-vs-entrypoint/)
- [Docker ENTRYPOINT Vs CMD Explained With Examples, DevOpsCube](https://devopscube.com/entrypoint-vs-cmd-explained/)
