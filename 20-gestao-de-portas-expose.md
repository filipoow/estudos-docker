# Gestão de portas: a instrução EXPOSE

## Um ponto que costuma confundir: EXPOSE não abre nada

A instrução `EXPOSE` é, provavelmente, a instrução de Dockerfile mais mal compreendida entre iniciantes, porque seu nome sugere uma ação que ela não realiza de fato. Vale esclarecer isso logo de início: `EXPOSE` não expõe porta nenhuma para fora do container, ela é, na prática, só documentação.

## O que EXPOSE realmente faz

```dockerfile
EXPOSE 80
```

Essa instrução comunica, para quem for ler o Dockerfile (ou para ferramentas que processam essa informação automaticamente), que a aplicação dentro do container escuta na porta 80. É metadado, uma informação declarativa, sem nenhum efeito prático imediato sobre como o container se comunica com o mundo externo.

## O que de fato conecta uma porta do container ao host: a opção -p

Quem realmente torna uma porta acessível de fora do container é a opção `-p` (ou `--publish`) do `docker run`, já usada de forma implícita em exemplos anteriores deste repositório:

```
docker run -p 8080:80 minha-imagem
```

Esse comando mapeia a porta 80 dentro do container para a porta 8080 na máquina hospedeira, e funciona independentemente de o Dockerfile ter ou não uma instrução `EXPOSE`. É perfeitamente possível publicar uma porta que nunca foi declarada com `EXPOSE`, e é igualmente possível declarar uma porta com `EXPOSE` e nunca publicá-la de fato com `-p`.

## Então por que usar EXPOSE, se não é obrigatório

Mesmo sem efeito técnico direto, `EXPOSE` continua valendo a pena por dois motivos práticos. O primeiro é documentação: qualquer pessoa lendo o Dockerfile entende imediatamente em qual porta aquela aplicação espera receber conexões, sem precisar vasculhar o código da aplicação para descobrir. O segundo é a opção `-P` (maiúscula) do `docker run`, que publica automaticamente todas as portas declaradas com `EXPOSE`, escolhendo portas aleatórias disponíveis no host para cada uma:

```
docker run -P minha-imagem
```

## Um resumo direto da diferença

`EXPOSE`, no Dockerfile, é uma declaração de intenção, sobre quais portas a aplicação usa. `-p`, no `docker run`, é a ação real, que de fato conecta uma porta do container a uma porta acessível de fora dele. Confundir os dois é um erro comum, mas entender essa distinção evita bastante frustração na hora de descobrir por que um serviço "não está acessível", mesmo com o `EXPOSE` presente no Dockerfile.

## Fontes

- [What's the Difference Between Exposing and Publishing a Docker Port?, How-To Geek](https://www.howtogeek.com/devops/whats-the-difference-between-exposing-and-publishing-a-docker-port/)
- [Publishing and exposing ports, Docker Docs](https://docs.docker.com/get-started/docker-concepts/running-containers/publishing-ports/)
- [Docker EXPOSE Ports, vsupalov.com](https://vsupalov.com/docker-expose-ports/)
