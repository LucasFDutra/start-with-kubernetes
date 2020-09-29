# **EM ANDAMENTO**
PRECISEI PAUSAR ESSE REPOSITÓRIO POR UM TEMPO. ASSIM QUE DER EU VOLTO

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
                value: "8080" # precisa ser uma string
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

> A PASTA DE REFERÊNCIA É A PASTA [02-SERVICES](./02-SERVICES)

Services são um conjunto lógico de pods e uma política pela qual saberemos como vamos acessar estes pods.

Ou seja, o service funciona como uma interface entre o acesso e o pod.

Agora como vamos fazer para definir quais pods pertêncem a um dado serviço? bom, é só a gente referenciar os pods, mas como fazemos isso? Por meio de labels.

## 5.1 LABELS

> A PASTA DE REFERÊNCIA É A PASTA [02-SERVICES/ex-01](./02-SERVICES/ex-01)

Utilizarei o pod `pod-node-simple-api` que criamos no exemplo anterior. No arquivo manifesto que utilizamos para criar esse pod, vamos definir uma label para ele. Essa label recebera o nome de `app`, e terá o valor de `simple-api`, e fica dentro de `metadata`.

- Em yaml
    ```json
    {
        "apiVersion": "v1",
        "kind": "Pod",
        "metadata": {
            "name": "api-pod",
            "labels": {
                "app": "simple-api"
            }
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

- Em json
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
        name: api-pod
        labels:
        app: simple-api
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

Agora para aplicarmos essa modificação no pod, vamos rodar o comando:

```sh
$ microk8s.kubectl apply -f 02-SERVICES/ex-01/pod-node-simple-api.yaml
```

Se rodarmos agora o comando 

```sh
$ microk8s.kubectl describe pod api-pod
```

Vamos ver todas as informações desse pod, e dentre elas veremos `Labels:       app=simple-api`

Uma coisa importante sobre labels é que o mesmo pod pode ter várias, e quando formos criar um service, devemos nos referenciar a essas labels, sendo que para um pod entrar em um grupo do service ele deve possuir todas as labels que são descritas no service. Em exemplo seria:

pod|labels
|-|-|
|pod-api-1| A,B,C|
|pod-api-2| A,B,C|
|pod-api-3| A,B,D|

Se criarmos um serviço que busque por labels A e B e C então somente os pods 1 e 2 entrarão nesse service.


### 5.1.1 BOAS PRÁTICAS

Para escrever boas labels, temos algumas boas práticas a seguir:

- Labels para definir o tipo de ambiente:
    - env=prod, env=dev

- Labels para versão:
    - version=1.2

- Labels para nome do serviço:
    - app-name=api-naruto

- Hash do último commit daquele código, para controle de versão específico:
    - commit=7abdee34

- Agrupamento de projetos:
    - project=api-animes


### 5.1.2 OUTROS USOS PARA LABELS

Além de servirem para agrupar pods em um service, elas podem ser utilizadas na flag -l do kubectl. Por exemplo, rode o comando 

```sh
$ microk8s.kubectl get pod -l app=simple-api
```

Isso vai trazer informações somente de pods com essa label (no nosso caso, somente um).

> OBS.: Também é possível passar multiplas labels, `$ microk8s.kubectl get pod -l "app=simple-api, commit=7abdee34"`

## 5.2 DEFININDO UM SERVICE

> A PASTA DE REFERÊNCIA É A PASTA [02-SERVICES/ex-02](./02-SERVICES/ex-02)

Agora que já entendemos para que serve um service. Vamos criar um.

O processo é bem semelhante ao de criar um pod, e envolve criarmos um arquivo manifesto.

- Em yaml
```yaml
apiVersion: v1,
kind: Service,
metadata: 
    name: api-pod-svc
spec: 
    selector: 
        app: simple-api
    ports:
        - protocol: TCP
          port: 8085
          targetPort: 8080
```

- Em json
```json
{
    "apiVersion": "v1",
    "kind": "Service",
    "metadata": {
        "name": "api-pod-svc"
    },
    "spec": {
        "selector": {
            "app": "simple-api"
        },
        "ports": [{
            "protocol": "TCP",
            "port": 8085,
            "targetPort": 8080
        }]
    }
}
```

No inicio só mudamos o kind para Service, pois agora queremos criar um service.

- selector: Colocaremos qual o seletor dos pods, ou seja, quais labels esse service irá compreender.

- Ports: É onde definimos as configurações de comunicação do service para com os pods e o restante do mundo.

    - protocol: Pode ser TCP, UDP ou SCTP (TCP é o default, veja sobre os outros para entender a diferença).
    
    - port: É a porta que o service vai ouvir, ou seja, a porta que será exposta para a internet.

    - targetPort: É a porta de destino, dentro do pod, deve ser a mesma que utilizamos no campo `containerPort` do pod.

Mas agora imagine que precisamos modificar a porta do nosso pod, vamos precisar modificar no pod e no service que ele está atrelado. Mas isso não é exatamente necessário, podemos dar um apelido para a porta do pod, e chamar o apelido no service, sendo assim, vamos poder trocar a porta do pod sem precisar trocar no service também, basta mantermos o apelido dela. Para isso vamos fazer uma modificação no arquivo do pod (esse arquivo modificado ficará dentro de ex-02, assim mantenho o ex-01 intocado) o campo `port` que receberá um novo campo chamado `name`.

- Em yaml
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
        name: api-pod
        labels:
        app: simple-api
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
                name: porta-api
    ```

- Em json
    ```json
    {
        "apiVersion": "v1",
        "kind": "Pod",
        "metadata": {
            "name": "api-pod",
            "labels": {
                "app": "simple-api"
            }
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
                    "containerPort": 8080,
                    "name": "porta-api"
                }]
            }]
        }
    }
    ```

Agora vamos modificar o arquivo do service também, para isso basta modificar o `targetPort` para apontar para o `name` dado para a `port` do `pod`.

- Em yaml
    ```yaml
    apiVersion: v1
    kind: Service
    metadata: 
        name: api-pod-svc
    spec: 
        selector: 
            app: simple-api
        ports:
            - protocol: TCP
            port: 8085
            targetPort: porta-api
    ```


- Em json
    ```json
    {
        "apiVersion": "v1",
        "kind": "Service",
        "metadata": {
            "name": "api-pod-svc"
        },
        "spec": {
            "selector": {
                "app": "simple-api"
            },
            "ports": [{
                "protocol": "TCP",
                "port": 8085,
                "targetPort": "porta-api"
            }]
        }
    }
    ```

Antes de prosseguirmos dando comandos que nem doidos, vamos precisar entender como funcionam as redes internas do K8S.

### 5.2.1 CRIANDO UMA REDE

Se pararmos para pensar um pouco, o service nada mais é do que uma coisa que gerencia diversos pods separadamente, ou seja, vamos chegar no service e pedir para ele "ou, me manda para o pod-a" e ele vai ter que se virar para isso, mesmo que ele gerencie 30 pods. Porém esse trabalho não vai ser exatamente dele, e sim de um proxy reverso, que tem justamente essa função de entender qual requisição está chegando e para onde ele deve mandar a mesma, ou seja, um servidor de rotas.

A vantagem de um proxy reverso é que sem ele as requisições teriam que acontecer por uma url própria, ou seja, se tivéssemos os pods A, B e C, cada um teria sua própria URL, e sempre que mandássemos uma requisição ela teria que ir direto para a URL do pod responsável. Com um proxy reverso podemos melhorar isso, o proxy reverso possibilita a utilização de DNS ou IP. E através deste endereço o servidor saberá rotear todas as nossas requisições para o pod correto.

> OBS.: Isso aqui é necessário para quando estiver rodando local, provavelmente em cloud já terá esse serviço todo configurado para você, mas é bom saber isso, pois vai que uma hora precisa.

O proxy reverso que vamos utilizar é o NGINX, mas não vamos instalar ele e configurar tudo do zero, pois esse serviço é bem comum, e claro que alguém na comunidade já fez um compilado para gente. E esse é o [nginx-ingress-controller](https://github.com/kubernetes/ingress-nginx/). O qual vamos instalar com o seguinte comando:

```sh
$ microk8s.enable ingress 
```

Você vai ver que ele retorna que `serviceaccount/nginx-ingress-microk8s-serviceaccount created`. 

Se digitar agora o comando: 
```sh
$ microk8s.kubectl get pods --all-namespaces
```

Vai ver que temos um namespace chamado ingress, cujo name é `nginx-ingress-microk8s-controller-24c6p`, espere até ele ser efetivamente criado (coluna status) e dai podemos prosseguir.

Se digitar o comando:

```sh
$ microk8s.kubectl get services --all-namespaces
```

Você verá que temos um namespace chamado `default`, o que significa que agora temos um local padrão para nossas apis serem acessadas.

Agora digite o comando

```sh
$ microk8s.kubectl cluster-info
```

Do retorno desse comando, pegue a url que estará em `Kubernetes master`
```sh
Kubernetes master is running at https://127.0.0.1:16443
```

Ignore a porta e também acesse com http, ou seja `http://127.0.0.1`, se você jogar esse endreço no seu navegador vai aparecer um 404. Mas era essa a ideia mesmo. Vamos seguir com a criação do nosso serviço agora.

### 5.2.1 VOLTANDO PARA A CRIAÇÃO DO SERVICE

Primeiro vamos recriar o nosso pod, já que fizemos modificações nele, para isso vamos primeiro deletar ele e depois recriar. E depois vamos criar nosso serviço.

```sh
$ microk8s.kubectl delete pod api-pod
$ microk8s.kubectl create -f 02-SERVICES/ex-02/pod-node-simple-api.yaml
$ microk8s.kubectl create -f 02-SERVICES/ex-02/pod-api-service.yaml
```

Agora você pode verificar se o service foi criado com sucesso digitando:

```sh
$ microk8s.kubectl get services
```

Agora vamos pegar o IP do service para podermos acessar o pod, para isso digite:

```sh
$ microk8s.kubectl describe service api-pod-svc
```

Agora com a saída, teremos o IP, e também podemos ver aquela porta que definimos em `port` no arquivo do service. Então basta pegar o IP mais a porta e acessar no browser `10.152.183.46:8085`. Deu certo né? bom, mas isso ai só acontece no mikrok8s, no minikube temos que fazer mais algumas coisas, e na real o processo dele está mais correto.

Primeiro vamos modificar o service para que ele seja do typo `NodePort`.

- Em yaml
    ```yaml
    apiVersion: v1
    kind: Service
    metadata: 
        name: api-pod-svc
    spec: 
        type: NodePort
        selector: 
            app: simple-api
        ports:
            - protocol: TCP
            port: 8085
            targetPort: porta-api
    ```

- Em json
    ```json
    {
        "apiVersion": "v1",
        "kind": "Service",
        "metadata": {
            "name": "api-pod-svc"
        },
        "spec": {
            "type": "NodePort",
            "selector": {
                "app": "simple-api"
            },
            "ports": [{
                "protocol": "TCP",
                "port": 8085,
                "targetPort": "porta-api"
            }]
        }
    }
    ```

Agora delete seu service, e recrie ele.

```sh
$ microk8s.kubectl delete service api-pod-svc
$ microk8s.kubectl create -f 02-SERVICES/ex-02/pod-api-service.yaml
```

Agora rode o describe novamente para pegar a porta que o service irá se comunicar, ela fica no campo `NodePort`.

> OBS.: Essa porta é criada aleatóriamente dentro do intervalo 30000-32767

```sh
$ microk8s.kubectl describe service api-pod-svc
```

No meu caso ficou `31856` e agora vamos pegar o ip do microk8s, com o comando:

```sh
$ microk8s.config 
```

E procure o campo `server`, que vai vir com um ip e uma porta, mas pode ignorar a porta e utilizar o que está em `NodePort`,  no meu caso fica: `http://192.168.42.247:31856`

> OBS.: Se jogar no browser o endereço do service e a porta 8085 ainda vai funcionar, mas faça desse jeito que mostrei depois, pois é o jeito correto, o outro acontece por conta de coisas em particular do microk8s, o jeito que ele funciona e como ele se comunica com a máquina.

Caso queira que a porta do NodePort seja fixa, basta especificar ela no em um campo `nodePort` dentro de `ports`

- Em yaml
```yaml
apiVersion: v1
kind: Service
metadata: 
    name: api-pod-svc
spec: 
    type: NodePort
    selector: 
        app: simple-api
    ports:
        - protocol: TCP
          port: 8085
          nodePort: 30526
          targetPort: porta-api
```

- Em json
```json
{
    "apiVersion": "v1",
    "kind": "Service",
    "metadata": {
        "name": "api-pod-svc"
    },
    "spec": {
        "type": "NodePort",
        "selector": {
            "app": "simple-api"
        },
        "ports": [{
            "protocol": "TCP",
            "port": 8085,
            "nodePort": 30526,
            "targetPort": "porta-api"
        }]
    }
}
```

# 6. INGRESS


Estou tendo problemas nessa parte, e o site da documentação do microk8s caiu. então não vou conseguir ver como faço funcionar

> A PASTA DE REFERÊNCIA É A PASTA [03-INGRESS/ex-01](./03-INGRESS/ex-01)

Como já disse antes, o ingress nos auxilia a nos comunicarmos com a internet, e trata as rotas para nós. Considerando que você habilitou o ingress do nginx, então vamos definir um ingress. 

- Em yaml
    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata: 
        name: api-ing
    spec: 
        backend: 
            serviceName: default
            servicePort: 80
        rules: 
            - host: minhaapi.info
            http:
                paths: 
                    - path: /teste
                    backend: 
                        serviceName: pod-api-svc
                        servicePort: 8085
    ```

- Em json
    ```json
    {
        "apiVersion": "extensions/v1beta1",
        "kind": "Ingress",
        "metadata": {
            "name": "api-ing"
        },
        "spec": {
            "backend": {
                "serviceName": "default",
                "servicePort": 80
            },
            "rules": [{
                "host": "minhaapi.info",
                "http": {
                    "paths": [{
                        "path": "/teste",
                        "backend": {
                            "serviceName": "pod-api-svc",
                            "servicePort": 8085
                        }
                    }]
                }
            }]
        }
    }
    ```

Rode o comando de criação:

```sh
$ microk8s.kubectl create -f 03-INGRESS/ex-01/pod-api-ingress.yaml
```

- host: Será o nome DNS que seu serviço vai ouvir;

- http.paths: aqui será onde definiremos os caminhos das URLs e também para onde elas vão apontar
    - path: será o caminho propriamente dito, se colocarmos /teste, estamos dizendo que vamos trabalhar com a URL minhaapi.info/teste;
    - backend: será o local que vamos definir para onde vamos levar a requisição;
        - serviceName: nome do serviço que vai receber a requisição;
        - servicePort: a porta que o serviço está ouvindo.

Ou seja, se colocarmos no navegador `http://minhaapi.info/teste` vamos ser direcionados para a service que está na porta 8085 e que por sua vez vai para o pod na 8080.

Veja que agora podemos definir diversas rotas, com esse ingress, e cada uma aponta para um service. Isso é muito bom.

Porém nada é tão maravilhoso e simples, como já deve ter notado até aqui, então se jogar essa url no seu navegador, você não vai ver sua aplicação funcionando. Isso porque precisamos configurar o DNS.

## 6.1 DNS

Vamos definir o DNS dentro do nosso arquivo `etc/hosts`, que é o primeiro local que tada requisição que fazemos fica, no windows isso fica em `C:\Windows\System32\drivers\etc\hosts`.

Vamos primeiro pegar o ip do microk8s, igual fizemos anteriormente (talvez ele tenha mudado).

```sh
$ microk8s.config
```

No meu caso, o ip é `192.168.0.108`, então vou adicioná-lo na minha tabela de DNS colocando a seguinte linha: `192.168.0.108   minhaapi.info`.

E agora acesse o endereço `http://minhaapi.info/teste`

> OBS.: Em provedores de cloud isso de dns e ingress são fornecidos pela própria cloud. Mas vamos ver mais para frente como trabalharemos com cloud.




