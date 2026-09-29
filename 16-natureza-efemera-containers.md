# A natureza efêmera dos containers e sua importância na escalabilidade

## Containers não deveriam ser tratados como algo permanente

Depois de todo o percurso deste bloco de aulas, criando, gerenciando e removendo containers, vale fechar com a ideia conceitual mais importante de todas: um container bem projetado deveria ser descartável. Ele não é pensado para ser cuidado e mantido individualmente por muito tempo, como se fosse uma máquina única e insubstituível, ele é pensado para ser criado, usado, e destruído sem nenhuma cerimônia, sendo substituído por um novo sempre que necessário.

## A metáfora do gado e dos animais de estimação

Uma metáfora bastante usada na indústria para explicar essa mudança de mentalidade é "gado, não animais de estimação" (cattle, not pets). Um servidor tratado como animal de estimação recebe um nome próprio, é cuidado individualmente, e sua perda é um problema sério, exigindo recuperação cuidadosa. Um servidor (ou container) tratado como gado, ao contrário, é intercambiável: se um deles apresenta problema, ele simplesmente é descartado e substituído por outro idêntico, sem nenhum esforço especial de recuperação daquele item específico.

Containers se encaixam naturalmente nessa segunda categoria, e é justamente essa característica, ser descartável sem custo, que abre as portas para escalar uma aplicação de forma prática e barata.

## Por que isso exige aplicações sem estado (stateless)

Para que um container realmente possa ser destruído e substituído sem problemas, ele não pode guardar, dentro de si mesmo, nenhuma informação que precise sobreviver além da vida daquele container específico. Dados importantes, como o conteúdo de um banco de dados, precisam viver fora do container, em volumes ou serviços externos dedicados a isso, não dentro da camada de escrita temporária do próprio container. Essa disciplina, evitar guardar estado importante dentro do container, é o que garante que apagar e recriar um container não signifique perder dados.

## Como isso se conecta à escalabilidade

Como uma imagem Docker, já apresentada no arquivo sobre [imagens versionadas](03-imagens-versionadas.md), é um modelo selado, versionado e idêntico toda vez que é usado, e como containers iniciam em milissegundos, apresentado no arquivo sobre a [relação entre containers e sistemas operacionais](08-containers-e-sistema-operacional.md), o custo de criar (ou descartar) um container se aproxima de zero. Isso é exatamente o que permite escalar uma aplicação simplesmente subindo mais réplicas idênticas de um mesmo container quando a demanda aumenta, e removendo essas réplicas extras quando a demanda cai de novo, sem nenhum esforço manual, a mesma lógica de escalonamento automático já apresentada no arquivo sobre [Kubernetes e orquestração](05-kubernetes-orquestracao.md).

## E na atualização de aplicações

Essa mesma natureza descartável também simplifica atualizações: em vez de modificar uma aplicação já em execução, um jeito arriscado e propenso a erro, a prática comum é simplesmente construir uma nova imagem com a versão atualizada, e substituir os containers antigos por novos, criados a partir dessa imagem nova. Se algo der errado, basta voltar a rodar containers da versão anterior da imagem, já que ela continua existindo, intacta, exatamente como estava antes.

## Fontes

- [Cattle vs Pets, DevOps Explained, Hava](https://www.hava.io/blog/cattle-vs-pets-devops-explained)
- [What is cattle not pets? Meaning, Examples, Use Cases, DevOpsSchool](https://www.devopsschool.nl/cattle-not-pets/)
- [Containerization. Ephemeral, Idempotent, and Immutable, Medium](https://medium.com/@h.stoychev87/containerization-docker-and-containers-8e8f28fd0694)
