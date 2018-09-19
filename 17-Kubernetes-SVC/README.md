### K8s Services

Serviços no Kubernetes são usados para fazer a parte de registro, descoberta e balanceamento de carga de serviços. Se você por exemplo precisa conectar uma aplicação com um banco de dados, você provavelmente o fará através dos `Service` deste banco de dados.
O service permite que você exponha a porta de um container através de uma outra porta, fazendo proxy, alem de permitir que o serviço seja acessado através de um nome.
Vamos supor que você tenha definido o serviço de um banco de dados MongoDB com o nome mongo, para acessar o serviço basta configurar a sua aplicação para usar a url `mongo:27017`
