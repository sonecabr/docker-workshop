# Criando o primeiro Container

Vamos agora usar os repositorios criados para brincar com o nosso primeiro container publico, para isso precisaremos entender um pouco do mecanismo.
Vamos precisar inicialmente de uma receita chamada Dockerfile, e nesse Dockerfile iremos informar o que queremos executar:
```
# Nginx-exemplo
#
# VERSION   0.0.1

FROM nginx:stable-alpine 
LABEL maintainer="Andre Rocha <devel.andrerocha@gmail.com>"
LABEL Description="Este é apenas uma imagem de exemplo fazendo uso de um nginx como base"
USER root
ENV HOME=/opt/minhapasta
RUN apk --no-cache add vim && \
    mkdir -p $HOME  
ADD paracopiar.txt $HOME/
COPY entrypoint.sh $HOME/entrypoint
RUN cd $HOME && \
    pwd && \
    chmod -R 755 $HOME && \
    ls -l $HOME/ && \
    chown nginx -R $HOME && \
    ls -l $HOME/

EXPOSE 80
STOPSIGNAL SIGTERM
CMD ["nginx", "-g", "daemon off;"]
```
Para compilar a nossa receita e transformar ela em uma imagem vamos usar o comando build:
```
sudo docker build -t sonecabrworkshop2/meuprimeirorepopublico:latest .
```
Vamos entender o comando:

-t é como apontamos o repositorio para o qual essa imagem irá ser enviada quando quisermos publica-la

sonecabrworkshop2/meuprimeirorepopublico:latest obedece à estrutura usuariodorepositorio/repositorio:tag

Sendo que a tag a tag representa a versão que você quer publicar e usar o alias `latest` é o mesmo que informar que essa é a versão padrão

A saída esperada deve ser:
```
Sending build context to Docker daemon  3.584kB
Step 1/11 : FROM nginx:stable-alpine
 ---> 4f481234b230
Step 2/11 : LABEL maintainer "Andre Rocha <devel.andrerocha@gmail.com>"
 ---> Using cache
 ---> 294aa757430a
Step 3/11 : LABEL Description "Este é apenas uma imagem de exemplo fazendo uso de um nginx como base"
 ---> Using cache
 ---> 06eae3347d15
Step 4/11 : USER root
 ---> Using cache
 ---> 9b0945589790
Step 5/11 : ENV HOME /opt/minhapasta
 ---> Using cache
 ---> 4dffc34f180f
Step 6/11 : RUN apk --no-cache add vim &&     mkdir -p $HOME
 ---> Using cache
 ---> 0c99da68ca9f
Step 7/11 : ADD paracopiar.txt $HOME/
 ---> Using cache
 ---> 3990abfe6960
Step 8/11 : RUN cd $HOME &&     pwd &&     chmod -R 755 $HOME &&     ls -l $HOME/ &&     chown nginx -R $HOME &&     ls -l $HOME/
 ---> Running in 1276bb702834
/opt/minhapasta
total 4
-rwxr-xr-x    1 root     root            28 Sep 22 11:30 paracopiar.txt
total 4
-rwxr-xr-x    1 nginx    root            28 Sep 22 11:30 paracopiar.txt
 ---> 45cead582249
Removing intermediate container 1276bb702834
Step 9/11 : EXPOSE 80
 ---> Running in c54759985bf8
 ---> b7260a5a950c
Removing intermediate container c54759985bf8
Step 10/11 : STOPSIGNAL SIGTERM
 ---> Running in 07448a0220e0
 ---> 17daa4555036
Removing intermediate container 07448a0220e0
Step 11/11 : CMD nginx -g daemon off;
 ---> Running in 2f589ec2e0b2
 ---> b08857b70ba6
Removing intermediate container 2f589ec2e0b2
Successfully built b08857b70ba6
Successfully tagged sonecabrworkshop2/meuprimeirorepopublico:latest

```
Vamos agora repetir o processo para a nossa versão privada:

```
cd meuprimeirorepositorioprivado
sudo docker build -t sonecabrworkshop2/meuprimeirorepoprivado:latest .
```


