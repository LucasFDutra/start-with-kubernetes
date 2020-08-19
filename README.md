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