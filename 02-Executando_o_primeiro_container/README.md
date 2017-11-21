__Vamos executar alguns containers__

Vamos executar o container oficial do Hello World. Para tando basta executar:

```
sudo docker run hello-world
```
A saida esperada será:


Vamos analisar o que aconteceu:

```
Unable to find image 'hello-world:latest' locally
```
Neste ponto o docker tentou executar uma imagem com o nome hello-world a partir da sua máquina e não encontrou tal imagem

```
latest: Pulling from library/hello-world
5b0f327be733: Pull complete 
Digest: sha256:1f19634d26995c320618d94e6f29c09c6589d5df3c063287a00e6de8458f8242
Status: Downloaded newer image for hello-world:latest
```
Neste ponto o docker efetuou o Pull (Download) da imagem em questão e adicionou uma cópia ao seu repositorio local

```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```
Neste ponto podemos ver o resultado da execução do container, que neste caso é um print no próprio console.

# Vamos analisar o ambiente local após esta execução
```
sudo docker ps -a
```
Como pode ser visto ele adicionou ao seu historico um container que aponta para a imagem hello_world
```
CONTAINER ID        IMAGE                                                                                COMMAND                  CREATED             STATUS                      PORTS                                   NAMES
7907be0a7a90        hello-world                                                                          "/hello"                 3 minutes ago       Exited (0) 3 minutes ago                                            upbeat_heisenberg
```
- Por que o status dela está como Exited? 
  - Isto aconteceu por que o container executou, printou a saida e não tinha nenhum processo em daemon para mante-lo em execução
- Como assim um processo em daemon? 
  - A execução do container é fortemente ligada ao processo que ele tem que executar, ou seja..se o processo estiver em execução o estado do container será 'em execução', se o processo morrer o container morre
  - Isso é bom ou ruim?
    - Isso é uma das coisas que torna o docker tão bacana, pois possibilita que você gerencie o seu container como seu processo alvo.
- Então eu deveria ter somente um processo em daemon no container? 
  - Idealmente sim


```
sudo docker images
```
Podemos ver também que a imagem do hello_world agora é listada no repositorio local
```
hello-world                                             latest                                                      05a3bd381fc2        9 days ago          1.84kB
```
A partir de agora quando executarmos esta imagem o docker não irá mais até o repositorio para fazer o download da mesma, possibilitando a execução mesmo sem acesso a internet

#Vamos executar um container que possui um damon para podermos testar algumas coisas a mais

```
sudo docker run --rm --name lavai-o-mysql mysql
```   
Novamente vamos ver que ele vai efetuar o download da imagem em questão
Diferente da imagem de hello_world agora o que veremos na saida do console é o log do mysql e é possivel notar que o seu console ficou preso ( de forma parecida com a execução de um binario qualquer)
Um detalhe novo aqui é o erro abaixo:
```
error: database is uninitialized and password option is not specified 
  You need to specify one of MYSQL_ROOT_PASSWORD, MYSQL_ALLOW_EMPTY_PASSWORD and MYSQL_RANDOM_ROOT_PASSWORD

```
Bom, o container do mysql quer que você informe uma senha de acesso, então vamos fazer isso usando enviromentos no comando através do alias -e
```
sudo docker run --rm --name lavai-o-mysql -e MYSQL_ROOT_PASSWORD=123mudar mysql
```
E agora podemos ver os logs do mysql saindo no console:
```
Initializing database
2017-09-22T00:05:30.309712Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2017-09-22T00:05:32.197520Z 0 [Warning] InnoDB: New log files created, LSN=45790
2017-09-22T00:05:32.695442Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2017-09-22T00:05:32.893460Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: c06d0276-9f29-11e7-88d3-0242ac110003.
2017-09-22T00:05:32.929978Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2017-09-22T00:05:32.943641Z 1 [Warning] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
...
```
Legal, mas e aquele --rm e --name na execução?
O --rm serve para informarmos ao executor que ele deve remover o container do meu disco após o container mudar o estado para 'Exited', isso evita que você precise limpar na mão depois que não precisar mais
O --name serve para que possamos dar um apelido para o container, afim de reaproveitar o mesmo no futuro

Belez, mas e agora? meu console ta preso :(
```
Ctrl+C
```
Ixi, ta preso ainda :(
Vamos ter que usar calibre mais alto:
Vai em outra aba do seu console e:
```
sudo docker stop lavai-o-mysql
```
Ufa..soltou :)
E se não soltasse?
```
sudo docker kill lavai-o-mysql
```

Ta bom..agora vamos executar novamente:
```
sudo docker run --rm --name lavai-o-mysql -e MYSQL_ROOT_PASSWORD=123mudar mysql
```
Vai agora em outra aba do seu console e:
```
sudo docker ps
```
Você deverá ver isso:
```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS               NAMES
ca78189e2190        mysql               "docker-entrypoint..."   About a minute ago   Up About a minute   3306/tcp            lavai-o-mysql

```
Legal...mas como eu executo esse cara sem deixar meu console preso?
 - Vamos usar para isso o parametro `-d` , com ele a gente informa ao docker que queremos que o container seja executado em daemon.
Vamos parar o container novamente:
```
sudo docker stop lavai-o-docker
```
E executar agora trocando o -rm por -d
```
sudo docker run -d --name lavai-o-mysql -e MYSQL_ROOT_PASSWORD=123mudar mysql
``` 
Diferente da execução anterior você deve ver na saida do console apenas um hash:
```
sudo docker run --rm --name lavai-o-mysql -e MYSQL_ROOT_PASSWORD=123mudar -d mysql
ec93ef35abb1844e424dddba4f9bfe3adff49a213c47294cd3ca3e9a0704db71

```
O que é esse hash?
 - É o id do seu container
Pra que serve?
 - Para varias coisas, vamos começar usando para acessar os logs
 ```
  sudo docker logs --tail=100 -f ec93ef35abb1844e424dddba4f9bfe3adff49a213c47294cd3ca3e9a0704db71
 ```
 Na saida do console você vai ver os logs da aplicação (neste caso o mysql)
 ```
 2017-09-22T00:16:33.100857Z 0 [Note] Forcefully disconnecting 0 remaining clients
2017-09-22T00:16:33.100862Z 0 [Note] Event Scheduler: Purging the queue. 0 events
2017-09-22T00:16:33.100890Z 0 [Note] Binlog end
2017-09-22T00:16:33.101397Z 0 [Note] Shutting down plugin 'ngram'
2017-09-22T00:16:33.101404Z 0 [Note] Shutting down plugin 'ARCHIVE'
2017-09-22T00:16:33.101406Z 0 [Note] Shutting down plugin 'partition'
2017-09-22T00:16:33.101408Z 0 [Note] Shutting down plugin 'BLACKHOLE'
2017-09-22T00:16:33.101410Z 0 [Note] Shutting down plugin 'MyISAM'
2017-09-22T00:16:33.101417Z 0 [Note] Shutting down plugin 'MRG_MYISAM'
2017-09-22T00:16:33.101421Z 0 [Note] Shutting down plugin 'INNODB_SYS_VIRTUAL'
2017-09-22T00:16:33.101423Z 0 [Note] Shutting down plugin 'INNODB_SYS_DATAFILES'
2017-09-22T00:16:33.101425Z 0 [Note] Shutting down plugin 'IN
...
```
 - Agora vamos usar o id para entrar no container (sim, entrar nele :D)
 ```
 Ctrl+C
 sudo docker exec -it ec93ef35abb1844e424dddba4f9bfe3adff49a213c47294cd3ca3e9a0704db71 /bin/bash
 ```
 Você deverã ver uma saida que lembra muito um acesso via ssh:
 ```
 root@ec93ef35abb1:/#
 ```
 Espera, mas o que é o exec -it?
  - exec é o comando para executar binarios no container (neste caso o /bin/bash)
  - it = Modo iterativo + tty, ele permite que vc traga para o console da sua maquina o console do container

E como eu saio agora?
 ```
 exit
 ```
E como eu paro o container?
Da mesma forma que antes ou usando o id, mas antes, uma curiosidade
```
sudo docker ps
```
A saida vai mostrar um id diferente
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
ec93ef35abb1        mysql               "docker-entrypoint..."   7 minutes ago       Up 7 minutes        3306/tcp            lavai-o-mysql
```
Mas que id é esse?
 - É o mesmo, so que cortado no 12 caractere =D
Eu posso usar esse id?
 - Pode
 ```
 sudo docker stop ec93ef35abb1
 ```
Pronto, o container ja foi parado novamente

Espera ae..e se eu quisesse acessar o mysql do meu container, como seria?
Aí entra outra feature que vamos exemplificar executando o container novamente:
```
sudo docker run --rm --name lavai-o-mysql -e MYSQL_ROOT_PASSWORD=123mudar -d mysql
```
Se vc verificar as portas disponiveis na sua maquina vai ver que a do mysql não subiu mesmo com o container rodando:
```
tcp        0      0 127.0.0.1:30900         0.0.0.0:*               LISTEN      1336/core       
tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN      8199/dnsmasq
tcp6       0      0 :::2377                 :::*                    LISTEN      1529/dockerd    
tcp6       0      0 :::7946                 :::*                    LISTEN      1529/dockerd    
```

E como acesso então?
Vamos para o container e subir novamente usando o alias -p (port binding), esse cara é responsavel por expor uma porta interna do contaier para a maquina host (neste caso a sua)

```
sudo docker stop lavai-o-mysql
sudo docker run --rm --name lavai-o-mysql -e MYSQL_ROOT_PASSWORD=123mudar -d -p 3306:3306 mysql
```
Agora podemos ver que apareceu a porta 3306 na nossa lista:
```
tcp        0      0 127.0.0.1:30900         0.0.0.0:*               LISTEN      1336/core       
tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN      8199/dnsmasq    
tcp6       0      0 :::2377                 :::*                    LISTEN      1529/dockerd    
tcp6       0      0 :::3306                 :::*                    LISTEN      27933/docker-proxy
tcp6       0      0 :::7946                 :::*                    LISTEN      1529/dockerd   
```
