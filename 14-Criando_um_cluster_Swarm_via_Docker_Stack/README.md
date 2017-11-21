__Criando um Cluster Swarm via Docker Stack__

![Docker swarm](docker-swarm.png)

Agora que temos o nosso cluster devidamente configurado, vamos tentar implantar alguns containers, mas desta vez usando o comando `docker stack`:

Vá até o nó manager do seu cluster e crie um docker-compose.yml:
```
version: "3.3"

services:
  wordpress:
    image: wordpress
    links:
     - mariadb:mysql
    environment:
     - WORDPRESS_DB_PASSWORD=123mudar
    ports:
     - "127.0.0.1:80:80"
    volumes:
     - ./code:/code
     - ./html:/var/www/html
  mariadb:
    image: mariadb
    environment:
     - MYSQL_ROOT_PASSWORD=123mudar
     - MYSQL_DATABASE=wordpress
    volumes:
     - ./database:/var/lib/mysql

networks:
```

Agora tente implantar os serviços via docker-stack:

```
sudo docker stack deploy -c docker-compose.yml wordpress-test
```

A saida deve ser similar a:
```
Creating network test_default
Creating service test_wordpress
Creating service test_mariadb

```

O que é o `-c` ?:
 - -c é o parametro para indicarmos o arquivo de compose que desejamos usar
Funciona de forma identica ao Docker Compose?:
 - Sim, a diferença é que ele vai distribuir os serviços pelo cluster swarm

Vamos validar?
```
sudo docker stack ls
```
E a saida deve ser:
```
NAME                SERVICES
test                2

```

E como vejo os serviços?:
```
sudo docker service ls
```
A saida deve ser:
```
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
kkn4k6h9ip6g        test_mariadb        replicated          0/1                 mariadb:latest      
snwrxwr0fsvr        test_wordpress      replicated          0/1                 wordpress:latest    *:80->80/tcp
```

E como vejo em que nó cada serviço foi implantado?
```
sudo docker service ps $(sudo docker service ls -q)
```
A saida deve ser similar a:
```
ID                  NAME                IMAGE               NODE                  DESIRED STATE       CURRENT STATE             ERROR                              PORTS
r4cvpf4d3uyk        test_mariadb.1      mariadb:latest      andre-Inspiron-5458   Ready               Rejected 1 second ago     "invalid mount config for type…"   
ymrdan32muoi        test_wordpress.1    wordpress:latest    andre-Inspiron-5458   Ready               Rejected 1 second ago     "invalid mount config for type…"   
2047x09ii726        test_mariadb.1      mariadb:latest      andre-Inspiron-5458   Shutdown            Rejected 6 seconds ago    "invalid mount config for type…"   
ktt014v0vib9        test_wordpress.1    wordpress:latest    andre-Inspiron-5458   Shutdown            Rejected 6 seconds ago    "invalid mount config for type…"   
x9iwjl1ti14r        test_mariadb.1      mariadb:latest      andre-Inspiron-5458   Shutdown            Rejected 11 seconds ago   "invalid mount config for type…"   
xgto219kpn68        test_wordpress.1    wordpress:latest    andre-Inspiron-5458   Shutdown            Rejected 11 seconds ago   "invalid mount config for type…"   
jk09afddovr6        test_mariadb.1      mariadb:latest      andre-Inspiron-5458   Shutdown            Rejected 16 seconds ago   "invalid mount config for type…"   
yqtwfz5xpb5q        test_wordpress.1    wordpress:latest    andre-Inspiron-5458   Shutdown            Rejected 16 seconds ago   "invalid mount config for type…"   
fdw2ft84h6n8        test_mariadb.1      mariadb:latest      andre-Inspiron-5458   Shutdown            Rejected 21 seconds ago   "invalid mount config for type…"   

```

E esse erro ae?
 - Bem observado, aparentemente temos um erro de parametrização do volume, perçeba que na listagem dos serviços também temos 0/1 no numero de replicas.

Vamos limpar a stack:
```
sudo docker stack em test
```
A saída deve ser:
```
Removing service test_mariadb
Removing service test_wordpress
Removing network test_default

```

Vamos alterar a nossa receita para:
```
version: "3.3"

services:
  wordpress:
    image: wordpress
    links:
     - mariadb:mysql
    environment:
     - WORDPRESS_DB_PASSWORD=123mudar
    ports:
     - "127.0.0.1:80:80"
    volumes:
     - code:/code
     - html:/var/www/html
    deploy:    
      mode: replicated
      replicas: 2
      endpoint_mode: vip
  mariadb:
    image: mariadb
    environment:
     - MYSQL_ROOT_PASSWORD=123mudar
     - MYSQL_DATABASE=wordpress
    volumes:
     - db-data:/var/lib/mysql
    deploy:    
      mode: replicated
      replicas: 1
      endpoint_mode: dnsrr

volumes:
 db-data:
 code:
 html:
networks:
  overlay:
```

Eita..quanta coisa nova!
Vamos em partes:
 - volumes:
   - Com esta opção vamos definir na receita que o docker irá montar volumes para cada um dos items na lista, é possivel ver estes volumes usando:
   ```
   sudo docker volume ls | grep test
   ```  
   A saida será:
   ```    
    local               test_code
    local               test_db-data
    local               test_html
   ```
- deploy:
  - Com este parametro é possível indicarmos para o Swarm de que forma queremos que cada serviço seja implantado:
    - mode: replicated:
      - Indica o modo de deploy, o mais comum é replicated, mas é possivel usar replicated e global, sendo que replicated é um serviço que terá replicas que podem conviver no mesmo nó e global indica que teremos apenas uma instancia por nó
      - replicas: 1
        - Indica o numero de instancias docker que queremos em cada serviço
      - endpoint_mode: vip
        - Indica que queremos acesso experto a porta do serviço via balanceador 
      - endpoint_mode: dnsrr
        - Indica que queremos acesso apenas entre serviços, sendo que o mesmo será feito sem balanceamento e usando a estrategia Round Robin

Vamos verificar se agora deu certo?
```
sudo docker service ls
```
A saida deve ser:
```
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
ibclxb43zqlu        test_mariadb        replicated          1/1                 mariadb:latest      
iv5bxl48024h        test_wordpress      replicated          2/2                 wordpress:latest    *:80->80/tcp
```
E agora vamos novamente tentar listar em que nó estamos implantados:
```
sudo docker service ps $(sudo docker service ls -q)
```
A saída deve ser:
```
ID                  NAME                   IMAGE               NODE                  DESIRED STATE       CURRENT STATE               ERROR                       PORTS
pnd2edcmuai3        test_wordpress.1       wordpress:latest    andre-Inspiron-5458   Ready               Ready 3 seconds ago                                     
w6gpu7te0o6m         \_ test_wordpress.1   wordpress:latest    andre-Inspiron-5458   Shutdown            Failed 4 seconds ago        "task: non-zero exit (1)"   
sd1yatqa6zd2         \_ test_wordpress.1   wordpress:latest    andre-Inspiron-5458   Shutdown            Failed 41 seconds ago       "task: non-zero exit (1)"   
tmri6elxgi03         \_ test_wordpress.1   wordpress:latest    andre-Inspiron-5458   Shutdown            Failed about a minute ago   "task: non-zero exit (1)"   
p3yku2vh99nk         \_ test_wordpress.1   wordpress:latest    andre-Inspiron-5458   Shutdown            Failed about a minute ago   "task: non-zero exit (1)"   
z8pfizd54ndf        test_mariadb.1         mariadb:latest      andre-Inspiron-5458   Running             Running 8 minutes ago                                   
jnwohtx3q7ha        test_wordpress.2       wordpress:latest    andre-Inspiron-5458   Running             Starting 1 second ago                                   
sf2x1t3go702         \_ test_wordpress.2   wordpress:latest    andre-Inspiron-5458   Shutdown            Failed 6 seconds ago        "task: non-zero exit (1)"   
jfa2s50xdajq         \_ test_wordpress.2   wordpress:latest    andre-Inspiron-5458   Shutdown            Failed 43 seconds ago       "task: non-zero exit (1)"   
cq13hn07hmco         \_ test_wordpress.2   wordpress:latest    andre-Inspiron-5458   Shutdown            Failed about a minute ago   "task: non-zero exit (1)"   
pbfuvzbig8gs         \_ test_wordpress.2   wordpress:latest    andre-Inspiron-5458   Shutdown            Failed about a minute ago  
```

Perceba que no historico temos duas instancias em `Running` para o wordpress e uma para o mysql


