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
This node joined a swarm as a worker
```
Vamos validar? Digite no primeiro nó (manager):
```
sudo docker node ls
```
O retorno esperado deve ser parecido com:
```
ID                            HOSTNAME               STATUS              AVAILABILITY        MANAGER STATUS
9ps65av20yorbax4n6dewbpvb *   andre-Inspiron-5458    Ready               Active              Leader
oqiuhp20jf84ywkmpys3xixo7     andre-Vostro-14-5480   Ready               Active              

```
Como sei se um nó é manager ou worker?
 - Basta observar o `MANAGET STATUS`, se ele estiver como Leader ou Reachable ele é um Manager, caso contrário é um Worker

Nós de Manager podem ter containers sendo executados?
 - Podem

Como eu troco o tipo do nó entre Manager e worker?
 - Vá no nó de worker e digite:
   ```
   sudo docker swarm leave
   ```
   O retorno esperado deve ser parecido com:
   ```
   Node left the swarm.
   ```
   Agora vamos pedir o token do tipo manager. Vá até o primeiro nó (manager atual) e digite:
   ```
   sudo docker swarm join-token manager
   ```
   O retorno esperado deve ser parecido com:
   ```
   To add a manager to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3ebgsyihv5ohiatik505v12267l9465gnpywllwpo61qebs5in-8q6fdkokno1vx9ehi0jimxu5m 192.168.0.75:2377

   ```
   Vamos então executar o comando no nó que desejamos adicionar, e o retorno esperado deve ser parecido com:
   ```
   This node joined a swarm as a manager.
   ```

   Vamos agora validar. Digite em qualquer um dos nós:
   ```
   sudo docker node ls
   ```
   
   O retorno esperado deve ser algoo parecido com:
   
   ```
   9ps65av20yorbax4n6dewbpvb    andre-Inspiron-5458   Ready   Active        Leader 
   oqiuhp20jf84ywkmpys3xixo7    andre-Vostro-14-5480  Down    Active        
   v79hc36abtt6ov69ewwfq5o91 *  andre-Vostro-14-5480  Ready   Active        Reachable
   ```

   Como é possivel ver acima, o nó `andre-Vostro-14-5480` está presente em duas linhas, em uma ele é mostrado como `Down` e outra como `Ready`. Isso acontece por que tivemos uma falha de comunicação na remoção do worker.
   E como eu limpo isso?
   ```
    sudo docker node rm oqiuhp20jf84ywkmpys3xixo7
   ```
   E executando validando novamente:
   ```
   sudo docker node ls
   ```
   Devemos ver:
   ```
   ID                           HOSTNAME              STATUS  AVAILABILITY  MANAGER STATUS
   9ps65av20yorbax4n6dewbpvb    andre-Inspiron-5458   Ready   Active        Leader
   v79hc36abtt6ov69ewwfq5o91 *  andre-Vostro-14-5480  Ready   Active        Reachable

   ```

Ok mas como eu uso isso para fazer deploy de instancias docker?
 - Vamos aprender isso no pŕoximo passo =D.

