__Publicando a primeira imagem__

Vamos agora disponibilizar a nossa imagem no Dockerhub, sendo que uma será publica e uma será privada. Para tal vamos fazer uso do comando `login e push`
Primeiro precisamos fazer o login como o usuario dono do repositorio em que queremos publicar
```
sudo docker login
```
Será solicitado o usuario:
```
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username (sonecabr): 
```
Após informar o usuário será solicitada a senha
```
Username (sonecabr): sonecabrworkshop2
Password: 
```
Após informar a senha a saida esperada deve ser:
```
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username (sonecabr): sonecabrworkshop2
Password: 
Login Succeeded
```
Agora vamos fazer o push da imagem

```
sudo docker push sonecabrworkshop2/meuprimeirorepopublico:latest
```
E a saída esperada dever ser similar a essa:
```
The push refers to a repository [docker.io/sonecabrworkshop2/meuprimeirorepopublico]
cdf34adc2cea: Pushed 
fbb61201f590: Pushed 
549925c4df22: Pushed 
20a13eb9c148: Pushed 
188769bb12d8: Pushed 
72ac6227f2fe: Pushed 
latest: digest: sha256:3944bb3b9f58aecf6eca5753bf15726fcb40aa4f0641628fabdcfe92cc1141a7 size: 1780
```

Agora vamos fazer o upload da imagem privada:
```
sudo docker push sonecabrworkshop2/meuprimeirorepoprivado:latest
```
E a saída deve ser similar a anterior:
```
The push refers to a repository [docker.io/sonecabrworkshop2/meuprimeirorepoprivado]
cdf34adc2cea: Mounted from sonecabrworkshop2/meuprimeirorepopublico 
fbb61201f590: Mounted from sonecabrworkshop2/meuprimeirorepopublico 
549925c4df22: Mounted from sonecabrworkshop2/meuprimeirorepopublico 
20a13eb9c148: Mounted from sonecabrworkshop2/meuprimeirorepopublico 
188769bb12d8: Mounted from sonecabrworkshop2/meuprimeirorepopublico 
72ac6227f2fe: Mounted from sonecabrworkshop2/meuprimeirorepopublico 
7cbcbac42c44: Mounted from sonecabrworkshop2/meuprimeirorepopublico 
latest: digest: sha256:3944bb3b9f58aecf6eca5753bf15726fcb40aa4f0641628fabdcfe92cc1141a7 size: 1780
```

Mas o que muda entre o repositório publico e o privado?

O repositorio publico poderá ser baixado por qualquer usuario com acesso ao Dockerhub

O repositorio publico somente poderá ser baixado pelo seu usuario.

Vamos testar?
```
sudo docker logout
Removing login credentials for https://index.docker.io/v1/
sudo docker pull sonecabrworkshop2/meuprimeirorepoprivado:latest
Error response from daemon: repository sonecabrworkshop2/meuprimeirorepoprivado not found: does not exist or no pull access
```
