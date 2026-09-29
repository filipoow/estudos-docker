# Executando containers de forma interativa e em background

## O container precisa de um terminal, ou de nenhum?

Nem todo container serve para o mesmo tipo de uso. Alguns são pensados para uma interação direta, como se fosse um terminal comum dentro deles. Outros são serviços de longa duração, que deveriam simplesmente rodar sozinhos, sem que ninguém precise ficar olhando. O `docker run` oferece opções específicas para cada um desses cenários.

## Modo interativo: `-i` e `-t`

```
docker run -it ubuntu /bin/bash
```

A opção `-i` ("interactive") mantém a entrada padrão (stdin) do container aberta, permitindo digitar comandos para dentro dele. A opção `-t` ("tty") aloca um pseudo-terminal, fazendo essa sessão se comportar visualmente como um terminal de verdade, com prompt, cores e tudo mais. Juntas, `-it` entregam uma experiência equivalente a abrir um terminal comum, só que rodando dentro do container, isolado do resto do sistema pelos mecanismos de namespaces já apresentados no arquivo correspondente.

## Modo background (detached): `-d`

```
docker run -d nginx
```

A opção `-d` ("detach") inicia o container em segundo plano, devolvendo o controle do terminal imediatamente, sem exibir a saída do container na tela nem esperar por interação. É o modo natural para serviços de longa duração, como um servidor web ou um banco de dados, que deveriam simplesmente ficar no ar, sem prender o terminal que os iniciou, o mesmo espírito por trás do `nohup`, já apresentado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/10-devops-monitoramento-e-processos-longos/06-nohup-screen-tmux.md).

## Combinando os dois: `-dit`

```
docker run -dit ubuntu
```

É possível combinar as três opções, iniciando o container em segundo plano, mas já preparado com entrada e terminal disponíveis, para que seja possível se conectar interativamente a ele depois, sempre que necessário.

## Voltando a um container: docker attach e docker exec

Para retomar uma sessão interativa dentro de um container que já está rodando em segundo plano, existem duas ferramentas com propósitos diferentes. O `docker attach` conecta o terminal atual diretamente ao processo principal do container, exatamente como se aquele processo estivesse rodando ali na hora. Para se desconectar sem encerrar nada, o atalho é `Ctrl+P` seguido de `Ctrl+Q`, funcionando só quando o container foi iniciado com `-it`.

Já o `docker exec` inicia um processo novo, adicional, dentro de um container que já está rodando, sem interferir no processo principal dele:

```
docker exec -it meu-app /bin/bash
```

Esse comando é extremamente comum na prática, usado para "entrar" num container em execução para investigar algo, sem correr o risco de derrubar o processo principal daquele container ao se desconectar depois.

## Fontes

- [Docker Detached Mode Explained, freeCodeCamp](https://www.freecodecamp.org/news/docker-detached-mode-explained/)
- [Attach and Detach From a Docker Container, Baeldung on Ops](https://www.baeldung.com/ops/docker-attach-detach-container)
- [Interactive vs Detached Execution Modes, Luis Llamas](https://www.luisllamas.es/en/docker-execution-modes-interactive-detached/)
