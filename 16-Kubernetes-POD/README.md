### Kubernetes POD

POD no K8s são como maquinas virtuais, eles podem contem 1 ou mais containers, sendo que estes containers compartilham recursos como se estivessem na mesma maquina.
É possivel usar duas abordagens para implantar um POD, através de uma receita de Pod ou uma receita de Deployment.
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    resources:
      limits:
        memory: "200Mi"
      requests:
        memory: "100Mi"
    ports:
    - containerPort: 80

```
Vamos agora detalhar esta receita:
 - ___apiVersion___ é a versão de api do K8s que vc pretende usar
 - ___kind:POD___ é para definir o tipo de receita, neste caso para um POD
 - ___metadata___ são dados para ajudar a definir tags e caracteristicas de implantação, é possivel por exemplo dar um nome para o POD alem de definir qual é a namespace em que ele será implantado
 - ___containers___ é a parte que define de fato quais containers serão criados quando um novo pod for lançado
 - ___containers.name___ é o nome que você deseja dara ao container
 - ___containers.image___ é a url para baixar a imagem docker (neste caso a partir do dockerhub)
 - ___containers.resources___ define quanto de processamento e memoria o container poderá usar ( em notação do k8s )
 - ___containers.ports___ define a porta do container que será exposta

 Agora se formos usar Deployment:
 ```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80

 ```
 Onde:
 ___kind: Deployment___ é para definir o tipo Deployment
 ___replicas:1___ define a quantidade de instancias que iremos lançar ( para o POD não para o container ), neste caso 1 instancia
 