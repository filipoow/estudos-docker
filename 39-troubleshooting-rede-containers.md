# Troubleshooting de rede em containers: portas, firewall e conectividade

## Um roteiro, em vez de tentativa e erro

Problemas de rede em containers costumam ter causas comuns, e investigá-los seguindo um roteiro, do mais simples ao mais específico, evita perder tempo mexendo em coisas ao acaso. O raciocínio é o mesmo já apresentado no [meu outro repositório de estudos de Linux](https://github.com/filipoow/estudos-linux/blob/main/05-rede-usuarios-seguranca/06-ping-nslookup-diagnostico.md) para diagnóstico de rede: isolar uma camada do problema de cada vez.

## 1. O container está rodando, e na rede certa?

O primeiro passo é conferir o básico: o container realmente está no estado Running, como apresentado no arquivo sobre o [ciclo de vida de um container](10-ciclo-de-vida-container.md)? E está conectado à rede esperada? O `docker network inspect`, apresentado no arquivo sobre [criação e gerenciamento de redes](36-docker-network-create-inspect-connect.md), lista exatamente quais containers estão conectados a cada rede, e é muito comum o problema ser simplesmente um container fora da rede onde deveria estar.

## 2. A aplicação está escutando na porta esperada?

Um container pode estar rodando e na rede certa, e ainda assim não responder, porque a aplicação dentro dele não está escutando onde se espera, ou está escutando só em `localhost` dentro do próprio container, inacessível de fora. Para conferir:

```
docker exec meu-container ss -tlnp
```

Esse comando executa, dentro do container, o `ss`, apresentado como substituto moderno do `netstat` no [outro repositório](https://github.com/filipoow/estudos-linux/blob/main/10-devops-monitoramento-e-processos-longos/05-netstat-monitoramento-rede.md), listando as portas em modo de escuta. Se a aplicação aparece escutando só em `127.0.0.1`, ela não vai aceitar conexões de outros containers, e precisa ser configurada para escutar em `0.0.0.0`.

## 3. A porta foi publicada corretamente?

Para acesso a partir do host ou de fora, a porta precisa ter sido publicada com `-p`, como visto no arquivo sobre [gestão de portas](20-gestao-de-portas-expose.md). O comando `docker port` mostra o mapeamento atual:

```
docker port meu-container
```

Um detalhe sutil: um mapeamento como `127.0.0.1:8080->80/tcp` significa que a porta só é acessível a partir da própria máquina, enquanto `0.0.0.0:8080->80/tcp` significa que é acessível de qualquer origem. Dois erros comuns nesse ponto: tentar publicar uma porta do host que já está em uso por outro processo, o que gera o erro "port is already allocated", e esquecer de publicar a porta de todo.

## 4. A resolução de nomes funciona?

Se um container não consegue alcançar outro pelo nome, vale lembrar a regra apresentada no arquivo sobre [redes customizadas e DNS interno](35-redes-customizadas-dns-interno.md): resolução de nomes só funciona em redes definidas pelo usuário, não na bridge padrão. De dentro do container, um `nslookup nome-do-outro-container` ajuda a confirmar se o nome está sendo resolvido.

## 5. Existe um firewall no caminho?

Firewalls na máquina hospedeira podem bloquear tráfego de e para containers, e o próprio Docker manipula regras de firewall para fazer a rede funcionar. Se tudo nos passos anteriores estiver correto, e a conexão ainda assim falhar, vale revisar as regras do firewall do host. Com `iptables`, regras que precisam se aplicar ao tráfego de containers devem ir na chain `DOCKER-USER`, que o Docker consulta antes das suas próprias regras, em vez de alterar as regras que o próprio Docker gerencia.

## 6. Testando de dentro da rede

Quando a causa continua obscura, as ferramentas apresentadas no arquivo sobre [testando comunicação com ping e curl](38-testando-comunicacao-ping-curl.md) permitem testar a conectividade de dentro da própria rede, o que ajuda a separar um problema de rede de um problema da aplicação: se um `curl` de dentro de outro container, na mesma rede, funciona, mas o acesso do host falha, o problema provavelmente está na publicação de portas ou no firewall, não na aplicação em si.

## Sintomas e causas comuns

"Connection refused" costuma indicar que a aplicação não está escutando, ou está na porta ou na rede errada. "Port is already allocated" indica um conflito de porta no host. "Could not resolve host" aponta para um problema de DNS, geralmente por estar usando a rede padrão em vez de uma rede definida pelo usuário.

## Fontes

- [Docker Network Troubleshooting: Diagnostics and Fixes for Container Connectivity, GNTech](https://blog.gntech.me/posts/2026-05-25-docker-network-troubleshooting-guide/)
- [Docker Networking Troubleshooting: Fix Container Connection Failures on Linux, The Back Room Tech](https://thebackroomtech.com/docker-networking-troubleshooting-linux/)
- [Troubleshooting Docker Networking Problems, dolpa.me](https://www.dolpa.me/troubleshooting-docker-networking-problems/)
