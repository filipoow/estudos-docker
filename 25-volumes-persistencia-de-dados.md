# A importância de volumes para persistência de dados

## O problema: tudo some quando o container é removido

O arquivo sobre a [natureza efêmera dos containers](16-natureza-efemera-containers.md) já deixou um aviso importante: dados importantes não deveriam viver dentro do container, porque containers são feitos para ser descartados. Este arquivo explica a solução prática para esse problema, e começa entendendo exatamente por que ele existe.

## A camada de escrita do container

Como apresentado no arquivo sobre [imagens versionadas](03-imagens-versionadas.md), uma imagem é composta por camadas somente leitura. Quando um container é criado a partir dela, o Docker acrescenta, por cima, uma única camada de escrita, própria daquele container. Tudo que o container escreve enquanto roda, um arquivo novo, uma linha num banco de dados, um upload de usuário, vai parar nessa camada.

O detalhe que importa: essa camada de escrita pertence ao container, e morre junto com ele. Se o container for removido com `docker rm`, como visto no arquivo sobre [removendo containers](15-removendo-containers.md), tudo que estava naquela camada desaparece junto, sem nenhuma possibilidade de recuperação. Isso é exatamente o comportamento esperado de algo descartável, e é também o motivo pelo qual rodar um banco de dados dentro de um container, sem nenhum cuidado extra, é uma receita para perder todos os dados na primeira atualização ou recriação.

## Volumes: dados que vivem fora do container

Um volume é um mecanismo de armazenamento gerenciado pelo próprio Docker, guardado fora do sistema de arquivos do container, e por isso independente do ciclo de vida dele. Quando um volume é conectado a um container, o container enxerga aquela pasta como se fosse parte do seu próprio sistema de arquivos, mas os dados escritos ali ficam guardados no volume, não na camada de escrita do container.

Na prática, isso significa que é possível remover o container, recriá-lo a partir de uma imagem nova, ou até conectar o mesmo volume a um container completamente diferente, e os dados continuam exatamente onde estavam. Em máquinas Linux, o Docker guarda esses volumes, por padrão, dentro de `/var/lib/docker/volumes`, seguindo a mesma lógica de organização de sistema de arquivos já apresentada no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/03-arquivos-permissoes-processos/01-estrutura-diretorios-fhs.md).

## Quando volumes se tornam indispensáveis

Qualquer dado que precise sobreviver a uma troca de container merece um volume: o conteúdo de um banco de dados, arquivos enviados por usuários, configurações geradas em tempo de execução, ou logs que precisam ser preservados para análise posterior. Sem isso, as atualizações de versão que o modelo de imagens versionadas torna tão simples, trocando containers antigos por novos, se tornariam uma operação destrutiva, apagando junto o estado acumulado pela aplicação.

## Fontes

- [Docker Volumes Explained: Stop Losing Data Every Time You Restart a Container, DEV Community](https://dev.to/teguh_coding/docker-volumes-explained-stop-losing-data-every-time-you-restart-a-container-254g)
- [Mastering Docker Volumes: A Complete Guide to Persistent Data in Containers, Medium](https://medium.com/devops-for-noobs/mastering-docker-volumes-a-complete-guide-to-persistent-data-in-containers-ef6a7ce11a8a)
- [Docker Volumes and Persistent Data: Complete Guide 2026, Khimananda](https://khimananda.com/blog/docker-volumes-and-persistent-data)
