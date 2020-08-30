# **EM ANDAMENTO**

> UMA DICA.: EU ESTOU LENDO UM LIVRO PARA ESTUDAR O ASSUNTO, E ESTOU GOSTANDO MUITO DELE, VOU DEIXAR O LINK AQUI, [Kubernetes: Tudo sobre orquestração de contêineres - Lucas Santos](https://www.amazon.com.br/Kubernetes-Tudo-sobre-orquestra%C3%A7%C3%A3o-cont%C3%AAineres-ebook/dp/B07X2MQL1Q/ref=sr_1_1?__mk_pt_BR=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=3T19KEEHXB35L&dchild=1&keywords=kubernetes+lucas+santos&qid=1597787059&sprefix=kubernet%2Caps%2C121&sr=8-1).

# 1. O QUE É O KUBERNETES
Considere que você tem um sistema com diversos containers, cada parte dele responsável por alguma parte do seu sistema. Agora imagine escalar isso tudo, verificar se algum container caiu, reativar um container que deu problema, fazer a conexão entra os containers... tudo na mão. Bom, não precisa imaginar um sistema muito grande para ter noção de que isso é uma péssima ideia, e que seria muito bom se tivesse um sistema automático para gerenciar seus containers de forma inteligênte e simples. E é justamente isso o que o kubernetes faz, ele orquestra seus containers.

## 1.1 CLUSTERS

Um cluster nada mais é do que um conjunto de máquinas, sendo que cada uma dessa máquinas contém um grupo de containers, o kubernetes utiliza essa estrutura para gerenciar a aplicação. E a arquitetura utilizada é a de `master - slave` (slave tb pode ser chamado de `node`),  máquina `slave` é onde as aplicações realmente estarão rodando, nelas estarão nossos containers, sendo que a estrutura em que se encontra um container é chamada de `pod`. Já a máquina `master` tem por função gerenciar as máquinas `slave`, cuidado desde a criação de novas `slaves` até a responsabilidade de manter o estado da aplicação (esse estado é o estado das máquinas que ele controla, assim como das aplicações dentro delas, ou seja, se a máquina está funcionando, se todos os containers ali dentro estão ok, essas coisas). E para manter todas essas informações, a master utiliza um banco de dados do tipo chave/valor chamado ETCD.

## 1.2 KUBECTL
Para nos comunicarmos com o um cluster, utilizamos a ferramenta de linha de comando chamada kubectl. Mas por hora não precisa instalá-la, mas caso queira, para instalá-la no linux utilize o comando:

```sh
$ snap install kubectl --classic
```

> OBS.: caso utilize outro sistema, ou não queira utilizar o snap, [aqui](https://kubernetes.io/docs/tasks/tools/install-kubectl/) tem outras formas de instalação

confirme a instalação com o comando 

```sh
$ kubectl version
```

## 1.3 ESTRUTURA DE UM NODE

Um node é composto por um ou mais containers, a engine do docker, e um kubelet. Sendo que o kubelet é um auxiliar da máquina `master`, ajudando-a a verificar aquele determinado nó. Pense da seguinte forma, imagine que você tem uma aplicação com milhares de nós (tipo a google), a sua máquina `master` pode ser parruda o quanto quiser, ele não vai dar conta de gerenciar tudo sozinha, por isso cada node tem um kubelet, que sempre está vendo se todos os containers que pré-configuramos que deveriam existir estão ali, se a saúde do nó está de acordo, repassar essas informações para o master e receber a ordem para executar as operações dentro daquele node, como por exemplo a de criar um novo container (se algum outro caiu).


## 1.4 RESUMÂO
O kubernetes nos possibilita um sistema:
- Fácil de configurar;
- Fácil de escalar, sendo que podemos aumentar o sistema somente onde realmente precisa, sem precisar aumentar todo o sistema (monólitos);
- Fácil de gerenciar;
- Fácilmente extensível, ou seja, podemos adicionar novos serviços tranquilamente;
- Extrai a máxima eficiência de cada máquina utilizada.


# 2. CONFIGURANDO O AMBIENTE DE DESENVOLVIMENTO

## 2.1 DOCKER

Instale o docker com os comandos abaixo:

```shell
sudo apt install docker.io
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo groupadd docker
sudo usermod -aG docker $USER
su $USER
```

Reinicie a máquina (talvez fazer logout já seja o bastante), para aplicar os comandos de usermod, que permitirá a execução dos comandos sem precisar ficar digitando `sudo`.

para confirmar a instalação, rode o comando:

```sh
$ docker --version
```

> OBS.: Caso não saiba usar o docker, não continue com seus estudos de kubernetes, vá aprender docker primeiro e depois venha aqui, eu também tenho um repositório para começar a trabalhar com docker, nesse link [aqui](https://github.com/LucasFDutra/start-with-docker).


## 2.2 KUBERNETES LOCAL

Para aprender o kubernetes é legal que primeiro você utilize ele localmente, assim você se habitua com os comandos, e a dinâmica de funcionamento dele, para isso existem duas ferramentas (que eu conheço) que fazem o serviço, uma delas é o `Minikube` que é desenvolvido pelo próprio kubernetes, e o `microk8s` da Canonical (a empresa do ubuntu).

A diferença entre eles é que o Minikube precisa que você instale o VirtualBox na sua máquina, já o microk8s não, ele cria os clusters sem você precisar instalar o VirtualBox ou qualquer outro serviço de virtualização. E também o minekube precisa que você instale o `kubectl` (então volte ai e pode instalá-lo agora caso queira utilizar o minikube), já o microk8s tem um comando próprio para se comunicar com os clusters, que é o `microk8s.kubectl` (sim, é só isso, o resto dos comandos é idêntico, tem até jeito de criar um alias para que sempre que digitar kubectl o seu pc entenda que está digitando microk8s.kubectl, assim fica identico ao kubectl original, isso se e somente se você não tiver uma instalação do kubectl na sua máquina). Por isso eu vou utilizar o microk8s para fazer as coisas localmente, visto que é mais simples de instalar e configurar, e não preciso instalar nenhuma VM no meu pc. Os comandos para instalar são:

- MINIKUBE: siga o tutorial presente na [documentação](https://kubernetes.io/docs/tasks/tools/install-minikube/)

- MICROK8S: 
    ```sh
    $ sudo snap install microk8s --classic --channel=1.18/stable
    $ sudo usermod -a -G microk8s $USER
    $ sudo chown -f -R $USER ~/.kube
    $ su - $USER
    ```
    Reinicie a máquina (talvez fazer logout já seja o bastante), para aplicar os comandos de usermod, que permitirá a execução dos comandos sem precisar ficar digitando `sudo`.
    

As diferenças entre os dois na hora de utilizar:

```sh
$ kubectl ....          #para minikube
$ microk8s.kubectl .... #para microk8s
```

Recomendo muito utilizar o microk8s pois a instalação do minikube pode ser meio chata, visto que precisa configurar muitas coisas, dentre elas uma vm.

> OBS.: Uma consideração a ser feita com o microk8s é que se você quiser fazer o alias basta colocar dentro do arquivo `~/.bash_aliases` o comando `alias kubectl='microk8s kubectl'`, mas isso somente pode ser feito caso não tenha instalado o `kubectl` propriamente dito. Pois a estratégia do microk8s é criar um namespace com o próprio kubectl, e assim acessá-lo com o comando `microk8s.kubectl`, para justamente evitar problemas com uma instalação previa do kubectl. Então caso não tenha uma instalação do kubectl você pode criar o alias. Eu não vou fazer isso, vou seguir utilizando o microk8s.kubectl pois prefiro assim, pois assim eu diferencio mais as coisas, e eu também já tenho uma instalação do kubectl.

# 3. CONTEXTO
Um contexto é o nome dado ao arquivo que contém os dados de conexão com um determinado cluster. Logo sempre que formos nos referir a um dado cluster temos que informar em qual contexto queremos trabalhar. Isso pode ser feito através de uma flag `--context <nome_do_cluster>` no final de cada comando, mas para facilitar e não precisarmos ficar passando esse parâmetro toda hora, podemos definir um contexto padrão.

## 3.1 CONFIGURANDO UM CONTEXTO PADRÃO
Para ver em qual contexto você está, digite o comando:
```sh
$ microk8s.kubectl config get-contexts
```

Na coluna CURRENT deve estar marcando algum dos seus clusters (provavelmente do microk8s). Para definir algum específico você pode digitar:

```sh
$ microk8s.kubectl config use-context <nome_do_cluster>
```
Depois disso feito, todos os comando que serão executados utilizarão esse contexto.

# 4. POD

> A PASTA DE REFERÊNCIA É A PASTA [01-POD](./01-POD)

Um pod é a menor unidade do nosso cluster, dentro de um pod teremos um ou mais containers. Não é usual deixar mais que um container em um pod, mas é possível fazer isso, talvez você encontre dois serviços que são altamente acoplados, e não é possível separar os dois.

Outra informação importante sobre pods é que eles são efêmeros, ou seja, uma vez que um pod para de funcionar (você termina ele ou ele cai sozinho) todas as informações que estavam neles, como os dados que foram gerados durante o processamento dos códigos, como por exemplo, csvs, são perdidos. Então jamais construa um pod com a finalidade de armazenar informações (banco de dados por exemplo).

## 4.1 CRIAR UM POD

> A PASTA DE REFERÊNCIA É A PASTA [01-POD/ex-01](./01-POD/ex-01)

Temos duas formas de criar um pod, uma delas é com a linha de comando, mas isso não é nada produtivo, a outra é com um arquivo manifesto, sendo que esse arquivo pode ser escrito no formato de `json` ou de `yaml` (particularmente eu prefiro o yaml, pois fica tudo igual ao docker, mas vou escrever sempre nos dois formatos aqui).

### 4.1.1 SOBRE O ARQUIVO MANIFESTO
Todos os arquivos manifestos seguem a mesma base:

- Em yaml
    ```yaml
    apiVersion: ""
    kind: ""
    metadata: 
        ""
    spec:
        ""
    ```

- Em json
```json
{
    "apiVersion": "",
    "kind": "",
    "metadata": {},
    "spec": {}
}
```

- apiVersion: É referente a versão da api do kubernetes que vai interpretar o arquivo.

- kind: É o tipo do workload que iremos criar, sendo que pode ser um pod, loadBalancer, service e outros (vamos chegar lá).

- metadata: são informações relativas ao workload que estamos criando, como por exemplo labels internas.

- spec: É onde de fato definiremos o nosso workload, como por exemplo qual contêiner vamos rodar nele, e as configurações desse contêiner.

### 4.1.2 ESCREVENDO O ARQUIVO

Para criar um pod, vamos nos basear em alguma imagem, e nesse caso irei escolher a imagem do mongodb. Logo nosso arquivo fica assim:

- Em yaml
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
        name: mongodb-pod
    spec:
        containers:
            - name: mongodb
              image: mongo
              ports:
                - containerPort: 27017
    ```

- Em json
    ```json
    {
        "apiVersion": "v1",
        "kind": "Pod",
        "metadata": {
            "name": "mongodb-pod"
        },
        "spec": {
            "containers" : [{
                "name": "mongodb",
                "image": "mongo",
                "ports": [{
                    "containerPort": 27017
                }]
            }]
        }
    }
    ```

veja que o `name` dentro de `metadata` é diferente do `name` de dentro do `containers`, No caso o `mongodb-pod` é o nome do pod, e o `mongodb` é o nome do contêiner, o qual vem de uma imagem do mongo. E o termo `ports` do container é a porta do container que será exposta na rede interna ao pod, veja bem, isso não é a porta que o pod está exposto ao restante do cluster.

### 4.1.3 CRIANDO O POD COM O ARQUIVO

depois de criar seu arquivo, digite no terminal o comando 
```sh
$ microk8s.kubectl create -f <caminho_do_arquivo/nome_do_arquivo>
```

ou seja, digite:

```sh
$ microk8s.kubectl create -f 01-POD/ex-01/pod-mongodb.yaml
# ou
$ microk8s.kubectl create -f 01-POD/ex-01/pod-mongodb.json
```

E para ver como o pod está, digite 

```sh
$ microk8s.kubectl get pods
```

E então verá o nome do pod, quantos de quantos estão prontos para uso, o status dele (running, ou creating), o numero de vezes que ele reiniciou, e o tempo que ele existe.

Para ver os logs gerados por esse pod, digite:

```sh
$ microk8s.kubectl logs <nome_do_pod>
```

ou seja, no nosso caso, digite:

```sh
$ microk8s.kubectl logs mongodb-pod
```

### 4.1.4 LIMITANDO RECURSOS DO POD
Como nos nunca vamos querer que um pod simplesmente tenha poder de consumir todo o nosso pc, vamos limitar os recursos que ele pode utilizar. Para isso vamos fazer uma alteração no nosso arquivo. Simplesmente configurando a tag `resources`.

- Em yaml
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
        name: mongodb-pod
    spec:
        containers:
            - name: mongodb
            image: mongo
            resources:
                requests:
                    cpu: 100m
                    memory: 128M
                limits:
                    cpu: 250m
                    memory: 256M
            ports:
                - containerPort: 27017
    ```

- Em json
    ```json
    {
        "apiVersion": "v1",
        "kind": "Pod",
        "metadata": {
            "name": "mongodb-pod"
        },
        "spec": {
            "containers" : [{
                "name": "mongodb",
                "image": "mongo",
                "resources": {
                    "requests": {
                        "cpu": "100m",
                        "memory": "128M"
                    },
                    "limits": {
                        "cpu": "250m",
                        "memory": "256M"
                    }
                },
                "ports": [{
                    "containerPort": 27017
                }]
            }]
        }
    }
    ```

dentro de `resources` temos as tags

- requests: Representa a configuração do contêiner para uma requeste a ele.

- cpu: Representa a quantidade de cpu que ele pode utilizar, sendo que vai de 0 a 1000m (milicores), essa unidade pode ser também de 0 a 1, mas não recomendo, pois o kubernetes pode ter problemas para interpretar casas decimais, então utilize de 0 a 1000m mesmo

- memory: Que é a quantidade de RAM que esse contêiner pode utilizar, no caso, ela pode ser definida em K, M, G, T, P, E todos referentes a unidades de memória, ou seja, kbytes, mega, giga, tera...

- limits: É a configuração máxima do nosso contêiner, se isso não existir ele poder variar do que foi configurado em requests até o máximo que a máquina fornecer.

> OBS.: Veja que o `requests` é o minimo de recursos, e o `limits` é o máximo.

Se um pod tentar ultrapassar os limites que foram estabelecidos, ele será automaticamente terminado.

Para modificar nosso pod, vamos primeiro deletar o já existente, e recriar o mesmo, então rode o comando para deletar um pod:

```sh
$ microk8s.kubectl delete pod mongodb-pod
```

E agora rode novamente o comando para criar o pod

```sh
$ microk8s.kubectl create -f 01-POD/ex-01/pod-mongodb.yaml
```

## 4.2 INTERAGINDO COM O POD
Se o pod é uma abstração para um contêiner, então podemos executar comandos dentro do pod assim como fazemos com contêiners, afinal, os mesmo são um sistema operacional completo. No nosso caso, temos um mongodb rodando dentro do pod, então vamos entrar no pod e executar comandos no mongo.

Para executar comandos dentro de um pod, podemos fazer o seguinte:
```sh
$ microk8s.kubectl exec -it <nome_do_pod> -- <comando>
```

ou seja:

```sh
$ microk8s.kubectl exec -it mongodb-pod -- mongo
```

Isso vai abrir o mongo de dentro do pod, para podermos dar os comandos do mesmo.

E vamos agora escrever um comando para inserir um registro no banco de dados `collectionExemplo` do mongo.

```sh
> db.collectionExemplo.insert({name: 'lucas'})
```

E depois podemos procurar esse registro que inserimos com o comando:

```sh
> db.collectionExemplo.find()
```

> OBS.: Nesse caso, estamos com um banco de dados dentro do pod, o que é algo que eu falei para não fazer, pois o pod não tem capacidade de manter dados caso venha a cair, mas isso é só um exemplo, então ta de boa.

## 4.3 CRIANDO NOSSA PRÓPRIA IMAGEM

> A PASTA DE REFERÊNCIA É A PASTA [01-POD/ex-02](./01-POD/ex-02)


Você pode criar uma imagem própria e colocá-la em um `container register`, que no nosso caso será o `docker hub`. E após subir essa imagem para lá, você pode utilizá-la no seu pod, assim como fizemos com a imagem do mongo.

Agora vamos criar o projeto, que será um servidor node bem simples. Para isso vamos ter uma pasta app, que dentro dela teremos nosso arquivo `index.js` e o arquivo `package.json`, para isso digite:

```sh
$ npm init -y
```

O arquivo index.js fica da seguinte forma:

- index.js
    ```JavaScript
    const http = require('http');

    const server = http.createServer((req, res) => {
        res.write('hello world');
        res.end();
    });

    server.listen(process.env.PORT);
    console.log(`Ouvindo na porta ${process.env.PORT}`);
    ```

Somente um servidor que retorna um hello world quando acessado na porta definida pela variável de ambiente.

Para facilitar as coisas vamos configurar um script no arquivo package.json, fazendo ele ficar da seguinte forma:

- package.json
    ```json
    {
    "name": "app",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "start": "node index.js"
    },
    "keywords": [],
    "author": "",
    "license": "ISC"
    }
    ```

Digite o seguinte comando no terminal:
```sh
$ export PORT=3000
$ npm start
```

Isso define que a variável ambiente `PORT` seja definida como 3000 e depois executamos o servidor na porta 3000, agora basta acessar o localhost nessa porta.

Mas voltando para o kubernetes, vamos criar o nosso container, e o mesmo deve ter uma imagem do node, para receber e rodar nosso código. O arquivo Dockerfile deve ficar fora da pasta `app` e deve ser da seguinte forma:

- Dockerfile
    ```yaml
    FROM node:12.18.3

    ADD app /app

    WORKDIR /app

    ENTRYPOINT [ "npm", "start" ]
    ```

Agora vamos criar a imagem que esse dockerfile representa:

```sh
$ docker build -t simple-node-api .
```

Para ver se a imagem foi criada corretamente pode digitar o comando:

```sh
$ docker images
```

Agora vamos riar o container com base nessa imagem para vermos que está tudo dando certo, e vamos juntamente com isso definir a variável de ambiente PORT e a porta que vamos nos comunicar com o container.

```sh
$ docker run -e PORT=8080 -p 2345:8080 simple-node-api
```

Isso faz com que nossa aplicação rode na porta 8080 do docker e que a acessaremos pela porta 2345 da nossa máquina. Caso queira testar basta acessar `http://localhost:2345`.

Visto que tudo está funcionando, vamos para publicar essa imagem no dockerhub. Para isso certifique-se de que tem uma conta lá, e vamos fazer o login no terminal, para isso digite:

```sh
$ docker login
```

E siga as instruções.

Agora vamos mandar a imagem para o duckerhub, digitando o seguinte comando:

```sh
$ docker tag <nome_da_imagem> <seu_dockerhub_user_name>/<nome_da_imagem>
$ docker push <seu_dockerhub_user_name>/<nome_da_imagem>
```

Ou seja:

```sh
$ docker tag simple-node-api lucasfdutra/simple-node-api
$ docker push lucasfdutra/simple-node-api
```

Agora se você entrar nos seus repositórios do dockerhub você vai ver sua imagem lá.

Agora vamos criar o arquivo declarativo para essa imagem.

- Em yaml
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
        name: api-pod
    spec:
        containers:
            - name: simple-api
            image: lucasfdutra/simple-node-api
            env:
                - name: PORT
                value: "8080"
            resources:
                requests:
                    cpu: 100m
                    memory: 128M
                limits:
                    cpu: 250m
                    memory: 256M
            ports:
                - containerPort: 8080
    ```

- Em json
    ```json
    {
        "apiVersion": "v1",
        "kind": "Pod",
        "metadata": {
            "name": "api-pod"
        },
        "spec": {
            "containers" : [{
                "name": "simple-api",
                "image": "lucasfdutra/simple-node-api",
                "env": [{
                    "name": "PORT",
                    "value": "8080"
                }],
                "resources": {
                    "requests": {
                        "cpu": "100m",
                        "memory": "128M"
                    },
                    "limits": {
                        "cpu": "250m",
                        "memory": "256M"
                    }
                },
                "ports": [{
                    "containerPort": 8080
                }]
            }]
        }
    }
    ```

Então rodaremos o comando:

```sh
$ microk8s.kubectl create -f 01-POD/ex-02/pod-node-simple-api.yaml
```

Digite o comando `microk8s.kubectl get pods` para ver o status do pod, caso ele esteja como running, então digite o comando `microk8s.kubectl logs api-pod` para ver que ele retornará `Ouvindo na porta 8080`, e agora se nos formos no browser e entrarmos na url `http://localhost:8080`, vamo ver absolutamente nada. Pois o que fizemos foi expor esse container/pod, ao node que ele se encontra, e não a internet em si (isso são cenas dos próximos capítulos).

# 5. SERVICES