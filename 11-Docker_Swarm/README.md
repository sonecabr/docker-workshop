__Docker Swarm__

![Docker Swarm](docker-swarm.png)

Docker Swarm nada mais é do que o orquestrador padrão do Docker. 
Ele está disponivel na instalação padrão do Docker e pode nos ajudar a criar, operar, escalar e recuperar um cluster de serviços implantados via Docker.

Como verifico a instalação?
```
sudo docker node ls
```
A sapida esperada deve ser o seu host listado como 'nó':
```
ID                            HOSTNAME              STATUS              AVAILABILITY        MANAGER STATUS
dg4rvqsxqwk3jhvcy1m9jdvun *   andre-Inspiron-5458   Ready               Active              Leader

``` 
O que significa `MANAGER STATUS` ?
 - Todo cluster Swarm precisa de um ou mais (idealmente 3) managers. Como apenas o seu host faz parte do cluster ele é automaticamente eleito o Manager.

Mas o que o manager faz?
 - O manager é responsavel por orquestrar o 'caos' dentro do cluster, ele é quem garante a saude dos demais nós, verifica status dos serviços e é através dele que podemos executar os comandos operacionais do Docker Swarm

O que significa `STATUS = Ready`?
 - Significa que este nó está operando e está saudável

O que significa `AVAIBILITY = Active`
 - Significa que este nó está atendendo neste momento. Os estados possiveis são: 
   - active: para nós atendendo
   - pause: para nós pausados pelo operador
   - drain: para nós que estão sendo removidos do cluster

