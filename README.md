Para utilizar o kubernetes na sua máquina instale o [MicroK8s](https://microk8s.io/) com o comando

```shell
$ sudo snap install microk8s --classic
```

vou utilizar a aplicação python bem simples que irá somente mandar um hello na rota `/` 

vou dar o comando 

```shell
$ docker build -t lucasfdutra/flask-hello .
```

agora precisamos subir essa imagem para algum lugar que hospeda a nossa imagem, nesse caso será o `docker hub`, então crie uma conta lá. e então rode o comando

```shell
$ docker login
```

e siga as instruções

agora digite o comando 

```shell
$ docker push lucasfdutra/flask-hello:latest
```

Criando o arquivo do kubernetes, que será o arquivo `flask-deployment.yml`

tendo o arquivo criado, rode o comando 

```shell
$ sudo microk8s kubectl apply -f flask-deployment.yml
```
isso já vai criar o seu cluster


para deletar o serviço

```shell
$ sudo microk8s kubectl delete services flask-hello-service
```

deletar deployments

```shell
$ sudo microk8s kubectl delete deployments flask-hello
```