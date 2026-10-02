# Os três tipos de mounts: Named Volumes, Bind Mounts e tmpfs

## "Volume" é só um dos jeitos de conectar armazenamento

O arquivo sobre [volumes e persistência de dados](25-volumes-persistencia-de-dados.md) apresentou o volume como a solução para dados que precisam sobreviver ao container. Na prática, o Docker oferece três tipos diferentes de mount (montagem), cada um com um comportamento e um uso ideal próprios. Entender a diferença entre eles evita escolher o tipo errado para cada situação.

## Named Volumes: gerenciados pelo Docker

Um named volume é exatamente o tipo apresentado até aqui: criado e gerenciado pelo Docker, guardado numa área própria do sistema de arquivos da máquina hospedeira, identificado por um nome. O Docker cuida de onde exatamente os dados ficam guardados, o usuário só precisa lembrar do nome.

É o tipo recomendado para dados persistentes de aplicações em produção, como bancos de dados, justamente porque o Docker controla a localização e o formato, tornando o volume independente da estrutura de pastas da máquina em que ele está. Isso também o torna portátil entre sistemas operacionais diferentes, funcionando de forma consistente em Linux, macOS e Windows.

## Bind Mounts: uma pasta específica do host

Um bind mount, ao contrário, conecta diretamente uma pasta (ou arquivo) específica da máquina hospedeira a um caminho dentro do container. Quem decide o caminho exato é quem roda o comando, o Docker não gerencia nada, só espelha aquela pasta para dentro do container.

A grande vantagem é a sincronização em tempo real: alterações feitas na pasta do host aparecem imediatamente dentro do container, e vice-versa. Isso torna o bind mount ideal para desenvolvimento, onde é conveniente editar o código no editor de texto do host e ver o resultado sem precisar reconstruir a imagem a cada mudança, retomando a preocupação com ciclos de build lentos já discutida no arquivo sobre [cache e múltiplos estágios](21-cache-e-multistage-builds.md).

A contrapartida é o acoplamento: um bind mount depende de um caminho específico existir na máquina hospedeira, o que o torna menos portátil. Em macOS e Windows, onde o Docker roda dentro de uma máquina virtual, como visto no arquivo sobre [Docker Engine e Docker Desktop](07-docker-engine-vs-docker-desktop.md), bind mounts também costumam ter desempenho menor que named volumes, já que cada acesso precisa atravessar a camada de virtualização.

## tmpfs Mounts: armazenamento só em memória

Um tmpfs mount guarda os dados na memória RAM da máquina, em vez de no disco. Os dados existem só enquanto o container estiver rodando, e desaparecem completamente quando ele para. É um mecanismo intencionalmente temporário, aprofundado no arquivo sobre [tmpfs](32-tmpfs-armazenamento-temporario.md).

## Qual escolher, em resumo

Dados persistentes que pertencem à aplicação, como um banco de dados, pedem named volumes. Arquivos que pertencem ao host e que precisam ser compartilhados com o container, como código-fonte em desenvolvimento ou arquivos de configuração, pedem bind mounts. Dados temporários, que nem deveriam sobreviver ao container, ou segredos que não devem tocar o disco, pedem tmpfs. Essa regra simples cobre a imensa maioria dos casos do dia a dia.

## Fontes

- [Docker Mount Types: Volumes, Bind Mounts & tmpfs Guide, DataCamp](https://www.datacamp.com/tutorial/docker-mount)
- [Docker Volumes vs Bind Mounts: Where Your Data Actually Lives, DEV Community](https://dev.to/jjoyneriv/docker-volumes-vs-bind-mounts-where-your-data-actually-lives-1ipl)
- [Docker Data Persistence: Volumes, Bind Mounts, and tmpfs Explained, Medium](https://medium.com/@software.engineer.notes/docker-data-persistence-volumes-bind-mounts-and-tmpfs-explained-8535d0747828)
