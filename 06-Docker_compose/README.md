__Docker compose__

O que é Dockercompose?
 - Docker compose é uma receita para criarmos deploy orquestrado no docker, este cara é usado normalmente em ambiente de desenvolvimendo e não é adequado a ambientes produtivos

Como assim não é adequado a ambiente produtivo?
 - O compose não sabe trabalhar com multiplos nós, sendo que em ambientes produtivos é bem comum distribuir a carga entre nós distintos para atingir premissas de disponibilidade, resiliencia e capacidade de deploy sem janela usando tecnicas de bluegreen ou canario.

Mas eu posso usar se quiser?
 - Sim pode, inclusive já presenciei amientes não criticos implantados via compose

Um ponto de atenção, as receitas de compose precisam seguir a versão correta para a versão do docker em que serão implantadas. Tal documentação pode ser encontrada na em:
https://docs.docker.com/compose/compose-file/ , hoje estamos na versão 3.

Como instalar o Docker Compose caso ele não esteja na ultima versão (1.16)
Na documentação oficial existe um passo a passo a ser seguido, vamos executar tais passos:
```
sudo curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` -o /usr/bin/docker-compose
sudo chmod a+x /usr/bin/docker-compose
sudo docker-compuse version
```
A saida esperada deve ser
```
docker-compose version 1.16.1, build 6d1ac21
docker-py version: 2.5.1
CPython version: 2.7.13
OpenSSL version: OpenSSL 1.0.1t  3 May 2016
```


