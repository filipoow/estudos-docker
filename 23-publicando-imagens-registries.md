# Publicando imagens Docker em registries

## O Docker Hub não é a única opção

O arquivo sobre o [Docker Hub](14-docker-hub.md) apresentou o registro público mais conhecido do ecossistema Docker, e usado por padrão sempre que nenhum outro é especificado. Mas o conceito de registry (registro) é mais amplo do que uma única plataforma: é qualquer serviço, público ou privado, dedicado a armazenar e distribuir imagens de container.

## Por que uma empresa optaria por um registro próprio

Publicar imagens no Docker Hub público funciona bem para projetos abertos, mas empresas frequentemente têm boas razões para preferir um registro privado: código proprietário que não deveria ficar acessível publicamente, integração mais direta com a infraestrutura de nuvem já em uso, ou requisitos de segurança e conformidade específicos do setor em que atuam.

Os principais provedores de nuvem oferecem seus próprios registros gerenciados: o Amazon ECR (Elastic Container Registry), o Google Artifact Registry, e o Azure Container Registry, cada um integrado naturalmente com os demais serviços daquele mesmo provedor. Também existe a opção de hospedar um registro próprio, dentro da infraestrutura da própria empresa, usando ferramentas como o Harbor.

## O fluxo geral de publicação

Independente do registro escolhido, o fluxo de publicação segue sempre os mesmos três passos gerais, já introduzidos de forma mais simples no arquivo sobre [Docker Hub](14-docker-hub.md):

**Autenticação**: antes de enviar qualquer imagem, é preciso autenticar o cliente Docker local contra aquele registro específico, geralmente através de um comando de login próprio de cada provedor.

```
docker login meu-registro-privado.com
```

**Marcação (tag)**: o Docker decide para onde enviar uma imagem com base no prefixo do próprio nome dela, então a imagem precisa ser marcada com o endereço completo do registro de destino.

```
docker tag minha-app meu-registro-privado.com/minha-app:1.0
```

**Envio (push)**: só depois de autenticado e corretamente marcado é que o envio de fato acontece.

```
docker push meu-registro-privado.com/minha-app:1.0
```

## Uma particularidade dos registros gerenciados na nuvem

Vale um detalhe prático para quem usa registros como o ECR: a autenticação normalmente não usa uma senha fixa, e sim um token temporário, gerado sob demanda e válido por um período limitado (frequentemente doze horas). Isso significa que scripts de automação de publicação, no mesmo espírito dos exemplos já apresentados no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/04-pacotes-scripts-automacao/05-integracao-scripts-cron-manutencao.md), costumam precisar de um passo extra, gerando esse token e autenticando com ele antes de cada publicação, em vez de guardar uma credencial fixa e reutilizável.

## Escolhendo o registro certo

Para projetos de código aberto, ou para experimentar e aprender, o Docker Hub continua sendo a escolha mais simples e direta. Para código proprietário e infraestrutura de produção real, um registro privado, seja gerenciado por um provedor de nuvem, seja autohospedado, costuma ser a escolha adequada, equilibrando controle, segurança e integração com o restante da infraestrutura já existente.

## Fontes

- [What is a registry?, Docker Docs](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-registry/)
- [Amazon ECR private registry, AWS Docs](https://docs.aws.amazon.com/AmazonECR/latest/userguide/Registries.html)
- [How to Self-host a Container Registry, freeCodeCamp](https://www.freecodecamp.org/news/how-to-self-host-a-container-registry/)
