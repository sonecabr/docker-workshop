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
RUN cd $HOME && \
    pwd && \
    chmod -R 755 $HOME && \
    ls -l $HOME/ && \
    chown nginx -R $HOME && \
    ls -l $HOME/

EXPOSE 80
STOPSIGNAL SIGTERM
CMD ["nginx", "-g", "daemon off;"]
