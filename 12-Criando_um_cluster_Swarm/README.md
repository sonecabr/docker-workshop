__Criando um Cluster Swarm__

![Docker swarm](docker-swarm.png)

Que tal criarmos um cluster swarm?
O que é eu preciso ter?
 - Alguns nós (máquinas que podem ser virtuais, na nuvem, ou outro pc que você tenha acesso)
 - Os nós precisam estar na mesma rede, ou pelo menos ter rota entre eles
 - Os nós precisam ter acl (acesso) tcp na porta 2375 entre eles

Vamos então iniciar o cluster:
Escolha um dos seus nós e inicie o cluster com o comando:
```
sudo docker swarm init
```
Ué, deu esse erro aqui:
```
Error response from daemon: This node is already part of a swarm. Use "docker swarm leave" to leave this swarm and join another one.
```
É possível que você já tenha um cluster swarm iniciado nessa maquina, vamos então ver como verificar?
```
sudo docker node ls
```
E a resposta esperada deve ser algo parecido com:
```
ID                            HOSTNAME              STATUS              AVAILABILITY        MANAGER STATUS
dg4rvqsxqwk3jhvcy1m9jdvun *   andre-Inspiron-5458   Ready               Active              Leader
```
Lembra do `MANAGER STATUS` ?. Sua máquina é o lider =D.

E como eu desfaço isso?
```
sudo docker swarm leave --force
```
E a resposta esperada deve ser algo parecido com:
```
Node left the swarm.

```
Vamos validar?
```
 sudo docker node ls
```
E o retorno deve ser:
```
Error response from daemon: This node is not a swarm manager. Use "docker swarm init" or "docker swarm join" to connect this node to swarm and try again.
```

Vamos então recriar o cluster?
```
sudo docker swarm init
```
E o retorno esperado deve ser algo parecido com:
```
Swarm initialized: current node (9ps65av20yorbax4n6dewbpvb) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-0vaeqvngdmupv3qib2hnc65jrwxiz818sywyw18zl0k70ujs79-2nsauw4j9d5d9i887l1a9a7zz 192.168.0.75:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

E o que é este token?
 - Este token é a sua chave para adicionar mais nós ao seu cluster
Seria só eu copiar e colar esse comando que ele deu?
 - Sim

Vamos fazer então?
Vá em outro nó e cole o comando que apareceu no seu retorno.
O retorno esperado deve ser algo parecido com:
```

```