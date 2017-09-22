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
Esta receita deve ser salva como 