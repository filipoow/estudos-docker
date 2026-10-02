# Boas práticas de segurança e gestão de dados persistentes

## Dados persistentes são o alvo mais valioso

Containers são descartáveis, mas os volumes onde os dados importantes vivem não são. Um invasor que compromete um container normalmente quer acesso ao que ele consegue alcançar, e se esse container tem um volume com dados sensíveis conectado, esses dados passam a estar ao alcance dele. Por isso, a segurança de volumes merece atenção própria, além dos cuidados gerais com containers.

## Princípio do menor privilégio, aplicado a volumes

Esse princípio, já apresentado em contexto de usuários e permissões no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/05-rede-usuarios-seguranca/03-seguranca-basica.md), vale igualmente aqui: cada container deveria ter acesso só ao que realmente precisa.

A aplicação mais direta é montar volumes em modo somente leitura sempre que o container só precisa ler os dados, nunca modificá-los. Isso foi mencionado de passagem no arquivo sobre [mapeamento de volumes com --mount](28-mapeando-volumes-mount.md), através da opção `readonly`:

```
--mount type=volume,source=configuracoes,target=/etc/app,readonly
```

Um container comprometido, com um volume montado em modo de leitura e escrita, poderia sobrescrever ou apagar dados críticos. Com o volume somente leitura, mesmo um container totalmente comprometido não consegue alterar aquele conteúdo, um ganho de segurança enorme para um custo praticamente nulo.

## Cuidado com bind mounts em pastas sensíveis

Como visto no arquivo sobre [tipos de mounts](27-tipos-de-mounts.md), um bind mount conecta uma pasta real do host ao container. Se essa pasta for sensível, como a raiz do sistema de arquivos, a pasta de configuração do sistema, ou o socket do próprio Docker, um container comprometido ganha acesso direto a ela. A recomendação geral é nunca expor pastas sensíveis do host através de bind mounts, preferindo named volumes, que ao menos escondem o caminho real no host e são gerenciados de forma isolada pelo Docker.

## Permissões dentro do volume

Os arquivos dentro de um volume continuam tendo dono e permissões, exatamente como em qualquer outro sistema de arquivos Linux, tema apresentado em detalhe no arquivo sobre [permissões chmod e chown](https://github.com/filipoow/estudos-linux/blob/main/03-arquivos-permissoes-processos/02-permissoes-chmod-chown.md) do outro repositório. Um erro comum é rodar o processo dentro do container como root, com acesso total aos arquivos do volume, quando um usuário comum, com permissões restritas, seria suficiente. Rodar a aplicação com um usuário sem privilégios dentro do container reduz o estrago possível caso ela seja comprometida.

## Dados sensíveis temporários: não escreva no disco

Para dados que são sensíveis mas não precisam persistir, como uma chave de API recebida em tempo de execução, o ideal é que eles nunca toquem o disco, nem mesmo dentro de um volume. Para esse caso, existe o tmpfs, apresentado no [próximo arquivo](32-tmpfs-armazenamento-temporario.md), que guarda os dados apenas em memória.

## Gestão: saber o que existe e o que está em uso

Uma boa higiene de dados persistentes inclui saber, a qualquer momento, quais volumes existem, o que cada um guarda, e se ainda estão em uso. Os comandos de listagem e inspeção, apresentados no arquivo sobre [criação e gerenciamento de volumes](26-criando-gerenciando-volumes.md), são a base disso. Combinados com uma estratégia de backup regular, apresentada no arquivo sobre [backup e restauração](29-backup-restauracao-volumes.md), e com a remoção periódica de volumes que já não servem para nada, apresentada no arquivo sobre [removendo volumes inativos](31-removendo-volumes-inativos.md), formam um ciclo completo e saudável de gestão de dados.

## Fontes

- [Best Practices for Secure Docker Containerization: Non-Root User, Read-Only Volumes, and Resource Sharing, Medium](https://medium.com/@maheshwar.ramkrushna/best-practices-for-secure-docker-containerization-non-root-user-read-only-volumes-and-resource-d34ed09b1bd3)
- [Docker Security: 14 Best Practices You Should Know, Better Stack](https://betterstack.com/community/guides/scaling-docker/docker-security-best-practices/)
- [What Is the Best Way to Manage Permissions for Docker Shared Volumes?, Baeldung on Ops](https://www.baeldung.com/ops/docker-shared-volumes-permissions)
