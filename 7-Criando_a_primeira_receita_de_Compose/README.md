
__Criando a primeira receita de Docker Compose__

Vamos agora criar uma receita simples de compose para fazer o deploy de dois serviços, sendo um wordpress e um mariadb que irá suportar esse wordpress (tal exemple é tambem demonstrado na documentação oficial da Docker).

Crie um arquivo com o nome docker-compose.yml e insira o conteudo:
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
  overlay:
```
Note que o arquivo segue o padrão yaml.

Vamos detalhar o conteudo do arquivo:
 - version 
   - trata-se da versão de receita que iremos usar, neste caso é o 3.3 que é suportado na versão 1.16 do Docker compose

 - services
   - Cada service representa um nó do seu cluster, nesse caso temos 2, de um wordpress e um mariadb
 - environment:
   -  Iremos informar as variaveis que queremos injetar na execução do container. Tem o mesmo efeito do `-e` que utilizamos anteriormente para setar a senha do ROOT
 - image
   - A imagem que iremos usar, se fosse a imagem que criamos anteriormente seria seurepo/meuprimeirorepopublico:latest
 - ports
   - A lista de portas que queremos expor. Tem o mesmo resultado do `-p` do `docker run`
 - networks
   - Vamos detalhar melhor esse assunto no futuro mas nesse ponto estamos informando a interface de rede que queremos usar para este serviço
 - deploy
   - Este atributo serve para informarmos caracteristicas de implantação do serviço (quantidade de instancias, e se o endopoint fica disponivel pro mundo ou se é interno)
   - mode:replicated
     - Estamos informando que queremos que ele cria instancias replicadas do serviço em questão
   - replicas: 1
     - Estamos informando que precisamos de apenas uma instancia do serviço
   - endpoint_mode:vip/dnsrr 
     - Quando usamos o tipo vip a porta do serviço é exposta para o mundo, quando usamos dnsrr estamos criando um balanceador interno que só responde para os serviços dentro do cluster, na pratica estamos dizendo nessa receita que a porta 80 do serviço de wordpress é exposta, e o mysql não
  - volumes
    - Vamos detalhar melhor no futuro, mas neste ponto estamos informando que onde os dados do container serão persistidos. Neste caso iremos persistir a pasta /var/lib/mysql/data do mysql para a pasta 
```
sudo docker-compose up
```
A saida esperada deve ser:
```
WARNING: Some networks were defined but are not used by any service: overlay
WARNING: The Docker Engine you're using is running in swarm mode.

Compose does not use swarm mode to deploy services to multiple nodes in a swarm. All containers will be scheduled on the current node.

To deploy your application across the swarm, use `docker stack deploy`.

Creating network "6dockercompose_default" with the default driver
Pulling mariadb (mariadb:latest)...
latest: Pulling from library/mariadb
aa18ad1a0d33: Already exists
fdb8d83dece3: Already exists
75b6ce7b50d3: Already exists
...
```
Ao final do processo será possivel acessar localmente o serviço do wordpress pelo browser no endereço 127.0.0.1
E a tela abaixo deve ser exibida:
![Wordpress and Compose](wp_compose.png)

Meu console ficou preso =/
```
Ctrl+C && \
sudo docker-compose up -d
```
E como monitoro agora?
```
sudo docker-compose ps
```
A saida deve ser:
```
WARNING: Some networks were defined but are not used by any service: overlay
           Name                         Command               State          Ports        
------------------------------------------------------------------------------------------
6dockercompose_mariadb_1     docker-entrypoint.sh mysqld      Up      3306/tcp            
6dockercompose_wordpress_1   docker-entrypoint.sh apach ...   Up      127.0.0.1:80->80/tcp
```

É possivel para os serviços? 
```
sudo docker-compose stop wordpress
```
A saida deve ser:
```
WARNING: Some networks were defined but are not used by any service: overlay
Stopping 6dockercompose_wordpress_1 ... done
```
Vamos verificar?
```
sudo docker-compose ps

WARNING: Some networks were defined but are not used by any service: overlay
           Name                         Command               State     Ports  
-------------------------------------------------------------------------------
6dockercompose_mariadb_1     docker-entrypoint.sh mysqld      Up       3306/tcp
6dockercompose_wordpress_1   docker-entrypoint.sh apach ...   Exit 0       
E como volto esse cara?
```

E como subo ele novamente?
```
sudo docker-compose start wordpress
```
A saida deve ser:
```
WARNING: Some networks were defined but are not used by any service: overlay
Starting wordpress ... done
```
Vamos verificar novamente?
```
sudo docker-compose ps
WARNING: Some networks were defined but are not used by any service: overlay
           Name                         Command               State          Ports        
------------------------------------------------------------------------------------------
6dockercompose_mariadb_1     docker-entrypoint.sh mysqld      Up      3306/tcp            
6dockercompose_wordpress_1   docker-entrypoint.sh apach ...   Up      127.0.0.1:80->80/tcp

```
Note que os dois serviços estão `UP`

E como limpo tudo?

```
sudo docker-compose down
```
A saída deve ser:
```
WARNING: Some networks were defined but are not used by any service: overlay
Stopping 6dockercompose_wordpress_1 ... done
Stopping 6dockercompose_mariadb_1   ... done
Removing 6dockercompose_wordpress_1 ... done
Removing 6dockercompose_mariadb_1   ... done
Removing network 6dockercompose_default
```
Vamos agora verificar uma ultima vez:
```
sudo docker-compose ps

WARNING: Some networks were defined but are not used by any service: overlay
Name   Command   State   Ports
------------------------------
```

Por enquando encerramos como Docker Compose =D, mas iremos revisitar esse cara para receitas mais rebuscadas alem de fazer uso da receita para fazer um deploy via `docker stack` que vai nos capacitar para ambientes produtivos