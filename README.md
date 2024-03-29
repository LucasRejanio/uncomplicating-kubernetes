# uncomplicating-kubernetes
Repository dedicated to the uncomplicated Kubernetes course by LinuxTips.

- Documentação oficial: https://kubernetes.io/
- Livro LinuxTip: https://livro.descomplicandokubernetes.com.br/pt/day_one/descomplicando_kubernetes.html#arquitetura-do-k8s

### O que é Kubernetes?

Kubernetes é um sistema de orquestração de contêineres open-source que automatiza a implantação, o dimensionamento e a gestão de aplicações em contêineres. Ele foi originalmente projetado pelo Google e agora é mantido pela Cloud Native Computing Foundation

### E o K8S?

O projeto Kubernetes foi desenvolvido pela Google, em meados de 2014, para atuar como um orquestrador de contêineres para a empresa. O Kubernetes (k8s), cujo termo em Grego significa "timoneiro", é um projeto open source que conta com design e desenvolvimento baseados no projeto Borg, que também é da Google 1. Alguns outros produtos disponíveis no mercado, tais como o Apache Mesos e o Cloud Foundry, também surgiram a partir do projeto Borg.

Como Kubernetes é uma palavra difícil de se pronunciar - e de se escrever - a comunidade simplesmente o apelidou de k8s, seguindo o padrão i18n (a letra "k" seguida por oito letras e o "s" no final), pronunciando-se simplesmente "kates".

### Componentes do Cluster

**Nodes:**

- Master: Inicializador do Cluster que por default não roda nenhuma aplicação além das dependencias do proprio Kubernetes.

- Worker: Maquinas dedicadas as aplicação do cluster.

**API Server:** 

É um dos principais componentes do k8s. Este componente fornece uma API que utiliza JSON sobre HTTP para comunicação, onde para isto é utilizado principalmente o utilitário kubectl, por parte dos administradores, para a comunicação com os demais nós, como mostrado no gráfico. Estas comunicações entre componentes são estabelecidas através de requisições REST;

**etcd:**

O etcd é um datastore chave-valor distribuído que o k8s utiliza para armazenar as especificações, status e configurações do cluster. Todos os dados armazenados dentro do etcd são manipulados apenas através da API. Por questões de segurança, o etcd é por padrão executado apenas em nós classificados como master no cluster k8s, mas também podem ser executados em clusters externos, específicos para o etcd, por exemplo;

**Scheduler**

O scheduler é responsável por selecionar o nó que irá hospedar um determinado pod (a menor unidade de um cluster k8s - não se preocupe sobre isso por enquanto, nós falaremos mais sobre isso mais tarde) para ser executado. Esta seleção é feita baseando-se na quantidade de recursos disponíveis em cada nó, como também no estado de cada um dos nós do cluster, garantindo assim que os recursos sejam bem distribuídos. Além disso, a seleção dos nós, na qual um ou mais pods serão executados, também pode levar em consideração políticas definidas pelo usuário, tais como afinidade, localização dos dados a serem lidos pelas aplicações, etc;

**Controller Manager:**

É o controller manager quem garante que o cluster esteja no último estado definido no etcd. Por exemplo: se no etcd um deploy está configurado para possuir dez réplicas de um pod, é o controller manager quem irá verificar se o estado atual do cluster corresponde a este estado e, em caso negativo, procurará conciliar ambos;

**Kubelet:**

O kubelet pode ser visto como o agente do k8s que é executado nos nós workers. Em cada nó worker deverá existir um agente Kubelet em execução. O Kubelet é responsável por de fato gerenciar os pods, que foram direcionados pelo controller do cluster, dentro dos nós, de forma que para isto o Kubelet pode iniciar, parar e manter os contêineres e os pods em funcionamento de acordo com o instruído pelo controlador do cluster;

**Kube-proxy:**

Age como um proxy e um load balancer. Este componente é responsável por efetuar roteamento de requisições para os pods corretos, como também por cuidar da parte de rede do nó;

**Container Runtime:**

O container runtime é o ambiente de execução de contêineres necessário para o funcionamento do k8s. Desde a versão v1.24 o k8s requer que você utilize um container runtime compativel com o CRI (Container Runtime Interface) que foi apresentado em 2016 como um interface capaz de criar um padrão de comunicação entre o container runtime e k8s. Versões anteriores à v1.24 ofereciam integração direta com o Docker Engine usando um componente chamado dockershim porém essa integração direta não está mais disponível. A documentação oficial do kubernetes (v1.24) apresenta alguns ambientes de execução e suas respectivas configurações como o containerd um projeto avaliado com o nível graduado pela CNCF (Cloud Native Computing Foundation) e o CRI-0 projeto incubado pela CNCF.

### Conceitos-chave do k8s
É importante saber que a forma como o k8s gerencia os contêineres é ligeiramente diferente de outros orquestradores, como o Docker Swarm, sobretudo devido ao fato de que ele não trata os contêineres diretamente, mas sim através de pods. Vamos conhecer alguns dos principais conceitos que envolvem o k8s a seguir:

**Pod:**

é o menor objeto do k8s. Como dito anteriormente, o k8s não trabalha com os contêineres diretamente, mas organiza-os dentro de pods, que são abstrações que dividem os mesmos recursos, como endereços, volumes, ciclos de CPU e memória. Um pod, embora não seja comum, pode possuir vários contêineres;

**Controller:**

é o objeto responsável por interagir com o API Server e orquestrar algum outro objeto. Exemplos de objetos desta classe são os Deployments e Replication Controllers;

**ReplicaSets:**

é um objeto responsável por garantir a quantidade de pods em execução no nó;

**DaemonSet:**

DaemonSet é um objeto Kubernetes que garante que uma cópia de um pod definido na configuração esteja sempre disponível em cada nó de trabalho em um cluster. Quando um novo nó é adicionado a um cluster, o DaemonSet aloca automaticamente o pod nesse nó

**Deployment:**

É um dos principais controllers utilizados. O Deployment, em conjunto com o ReplicaSet, garante que determinado número de réplicas de um pod esteja em execução nos nós workers do cluster. Além disso, o Deployment também é responsável por gerenciar o ciclo de vida das aplicações, onde características associadas a aplicação, tais como imagem, porta, volumes e variáveis de ambiente, podem ser especificados em arquivos do tipo yaml ou json para posteriormente serem passados como parâmetro para o kubectl executar o deployment. Esta ação pode ser executada tanto para criação quanto para atualização e remoção do deployment;

**Jobs e CronJobs:**

São objetos responsáveis pelo gerenciamento de jobs isolados ou recorrentes.


### Instalando Kubectl

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl

kubectl version --client
```

### Alias e autocomplete

```bash
source <(kubectl completion bash) # configura o autocomplete na sua sessão atual (antes, certifique-se de ter instalado o pacote bash-completion).

echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanentemente ao seu shell.
```

```bash
alias k=kubectl

complete -F __start_kubectl k
```

### Instalando Minikube

```bash
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

chmod +x ./minikube

sudo mv ./minikube /usr/local/bin/minikube

minikube version
```

**Comandos basicos:**

```bash
minikube start
kubectl get nodes
minikube ip
minikube ssh 
minikube start
minikube stop
minikube dashboard
minikube logs
```

### Iniciando um cluster

**Faça em todos os Nodes:**

1. Instalar o Docker:

```bash
curl -fsSL https://get.docker.com | bash
```

2. Adicione essa config no deamon do Docker:
``` bash
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
```

3. Execute:

```bash
mkdir -p /etc/systemd/system/docker.service.d

systemctl restart docker
```

```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

sudo echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update

sudo apt-get install -y kubeadm kubelet kubectl
```

**Execute no Node Master:**

1. Image pull

```bash
kubeadmin config images pull
```

2. Init Cluster:

```bash
kubeadmin init
```

3. Execute:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

4. Obtenha comando para criar os node:
```bash
kubeadm token create --print-join-command
```

**execute nos Workers:**

1. O resultado do ultimo comando do Master. Importante liberar a porta no Security Group do Master.

### Verificando a instalação
Para verificar se a instalação está funcionando, e se os nós estão se comunicando, você pode executar o comando kubectl get nodes no nó master, que deve lhe retornar algo como o conteúdo a seguir.

```
NAME        STATUS   ROLES    AGE   VERSION
node-01   Ready    master   8d    v1.19.1
node-02   Ready    <none>   8d    v1.19.1
node-03   Ready    <none>   8d    v1.19.1
```

### Primeiros passos no k8s

**Exibindo informações detalhadas sobre os nós**

```bash
kubectl describe node [nome_do_no]
```

Exemplo:

```bash
kubectl describe node node-02
```

Name:               node-02
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=elliot-02
                    kubernetes.io/os=linux
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true

**Verificando os namespaces e pods**

```bash
kubectl get namespaces
kubectl get pod -n kube-system
```

Exibir informações sobre um pod especifico:

```bash
kubectl describe pod <POD_ID> -n <NAMESPACE>
```

Será que há algum pod escondido em algum namespace? É possível listar todos os pods de todos os namespaces com o comando a seguir.

```bash
kubectl get pods --all-namespaces
```

Há a possibilidade ainda, de utilizar o comando com a opção -o wide, que disponibiliza maiores informações sobre o recurso, inclusive em qual nó o pod está sendo executado. Exemplo:

```bash
kubectl get pods --all-namespaces -o wide
```

### Executando nosso primeiro pod no k8s

```bash
kubectl run nginx --image nginx

pod/nginx created
```

**Validar criação do pod**

Você pode usar a flag de --dry-run-client para simular o apply do manifesto.

```bash
kubectl run nginx --image nginx --dry-run=client
```

Além disso, você pode usar o --dry-run para obter um manifesto sem "sujeira" do kubernetes:

```bash
kubectl run nginx --image nginx --dry-run=client -o yaml
kubectl run nginx --image nginx --dry-run=client -o yaml > nginx.yml
```

### Exportando um pod (Service)

Com o comando abaixo, o Kubernetes irá criar um service para expor o serviço para a porta declarada no manifesto.

```bash
kubectl expose pod nginx
```

**Tipos de services**

ClusterIP: IPs validos apenas dentro do Cluster.

NodePort: IPs expostos para internet atraves de IPs dos nodes junto com a porta aleatoria que o Kubernetes gera.

### Endpoint

Sempre que criamos um service, automaticamente é criado um endpoint. O endpoint nada mais é do que o IP do pod que o service irá utilizar, por exemplo, quando criamos um service do tipo ClusterIP temos o seu IP, correto?

Agora, quando batemos nesse IP ele redireciona a conexão para o Pod através desse IP, o EndPoint.

### Componentes do K8s simplificado
O k8s tem os seguintes componentes principais:

Master node
Worker node
Services
Controllers
Pods
Namespaces e quotas
Network e policies
Storage

**kube-apiserver**

é a central de operações do cluster k8s. Todas as chamadas, internas ou externas são tratadas por ele. Ele é o único que conecta no ETCD.

**kube-scheduller**

usa um algoritmo para verificar em qual node o pod deverá ser hospedado. Ele verifica os recursos disponíveis do node para verificar qual o melhor node para receber aquele pod.

**etcd**

No ETCD são armazenados o estado do cluster, rede e outras informações persistentes.

**kube-controller-manager**

é o controle principal que interage com o kube-apiserver para determinar o seu estado. Se o estado não bate, o manager irá contactar o controller necessário para checar seu estado desejado. Tem diversos controllers em uso como: os endpoints, namespace e replication.

**kubelet**

O kubelet interage com o Docker instalado no node e garante que os contêineres que precisavam estar em execução realmente estão.

**kube-proxy**
O kube-proxy é o responsável por gerenciar a rede para os contêineres, é o responsável por expor portas dos mesmos.

**supervisord**

Supervisord é o responsável por monitorar e restabelecer, se necessário, o kubelet e o Docker. Por esse motivo, quando existe algum problema em relação ao kubelet, como por exemplo o uso do driver cgroup diferente do que está rodando no Docker, você perceberá que ele ficará tentando subir o kubelet frequentemente.

**pod**

Pod é a menor unidade que você irá tratar no k8s. Você poderá ter mais de um contêiner por Pod, porém vale lembrar que eles dividirão os mesmos recursos, como por exemplo IP. Uma das boas razões para se ter mais de um contêiner em um Pod é o fato de você ter os logs consolidados.

O Pod, por poder possuir diversos contêineres, muitas das vezes se assemelha a uma VM, onde você poderia ter diversos serviços rodando compartilhando o mesmo IP e demais recursos.

**services**
Services é uma forma de você expor a comunicação através de um NodePort ou LoadBalancer para distribuir as requisições entre diversos Pods daquele Deployment. Funciona como um balanceador de carga.

### Comandos

![Kubernetes-Comandos](https://user-images.githubusercontent.com/52427398/182943867-201c98dd-fa3c-4432-9b7c-b90c38e72906.png)

### Container Network Interface
Para prover a rede para os contêineres, o k8s utiliza a especificação do CNI, Container Network Interface.

CNI é uma especificação que reúne algumas bibliotecas para o desenvolvimento de plugins para configuração e gerenciamento de redes para os contêineres. Ele provê uma interface comum entre as diversas soluções de rede para o k8s. Você encontra diversos plugins para AWS, GCP, Cloud Foundry entre outros.

Mais informações em: https://github.com/containernetworking/cni

Enquanto o CNI define a rede dos pods, ele não te ajuda na comunicação entre os pods de diferentes nodes.

As características básicas da rede do k8s são:

Todos os pods conseguem se comunicar entre eles em diferentes nodes;
Todos os nodes podem se comunicar com todos os pods;
Não utilizar NAT.
Todos os IPs dos pods e nodes são roteados sem a utilização de NAT. Isso é solucionado com a utilização de algum software que te ajudará na criação de uma rede Overlay. Seguem alguns:

- Weave https://www.weave.works/docs/net/latest/kubernetes/kube-addon/

- Flannel https://github.com/flannel-io/flannel/blob/master/Documentation/kubernetes.md

- Canal https://github.com/projectcalico/canal/tree/master/k8s-install

- Calico https://projectcalico.docs.tigera.io/about/about-calico

- Romana https://romana.io/

- Nuage https://github.com/nuagenetworks/nuage-kubernetes/blob/v5.1.1-1/docs/kubernetes-1-installation.rst

- Contiv https://contiv.io/


Mais informações em: https://kubernetes.io/docs/concepts/cluster-administration/addons/

### Replace object

```sh
kubectl replace -f deployment/nginx-limit.yaml
```

### Edit object

```sh
kubectl edit service nginx
```

### Kubectl taint

```sh
kubectl describe nodes elliot-01 | grep -i taint
Taints:             node-role.kubernetes.io/master:NoSchedule

kubectl describe nodes  | grep -i taints

kubectl taint node elliot-01 node-role.kubernetes.io/master:NoSchedule-

kubectl taint node elliot-01 node-role.kubernetes.io/master:NoSchedule

kubectl taint node elliot-02 key1=value1:NoSchedule

kubectl taint node elliot-02 key1=value1:NoSchedule-

kubectl get pods -o wide

kubectl taint node elliot-02 key1=value1:NoExecute-

kubectl taint node elliot-02 key1=value1:NoExecute

kubectl get pods -o wide

kubectl taint node all key1=value1:NoExecute
```

### Limits

O Kubernetes define Limits como a quantidade máxima de um recurso a ser usado por um container. Isso significa que o contêiner nunca pode consumir mais do que a quantidade de memória ou CPU indicada. As solicitações, por outro lado, são a quantidade mínima garantida de um recurso reservado para um contêiner.

### Volumes

#### emptyDir

Um volume emptyDir é criado pela primeira vez quando um Pod é atribuído a um nó e existe enquanto esse Pod estiver sendo executado nesse nó. Como o nome diz, o volume emptyDir está inicialmente vazio. Todos os contêineres no Pod podem ler e gravar os mesmos arquivos no volume emptyDir, embora esse volume possa ser montado no mesmo caminho ou em caminhos diferentes em cada contêiner. Quando um Pod é removido de um nó por qualquer motivo, os dados no emptyDir são eliminados permanentemente.

#### persistentVolumeClaim

Um volume persistentVolumeClaim é usado para montar um PersistentVolume em um Pod. PersistentVolumeClaims são uma forma de os usuários "solicitarem" armazenamento durável (como um GCE PersistentDisk ou um volume iSCSI) sem conhecerem os detalhes do ambiente de nuvem em particular.

Consulte as informações sobre PersistentVolumes para obter mais detalhes.

### CronJob

No Kubernetes, um CronJob é um tipo de recurso que permite executar tarefas em um horário agendado. Ele é semelhante ao utilitário Unix cron, que permite agendar tarefas para serem executadas em horários ou intervalos específicos.

### Secret e ConfigMap

"Secret" é um objeto que permite armazenar dados confidenciais, como senhas, chaves de API, certificados TLS e outros dados sensíveis. Os Secrets são usados para fornecer um mecanismo seguro para armazenar e distribuir informações confidenciais para os pods em um cluster. Os Secrets podem ser usados em diferentes formatos, como texto simples, Base64 ou arquivos criptografados.

Por sua vez, um "ConfigMap" é um objeto que permite armazenar dados de configuração, como variáveis de ambiente, arquivos de configuração, chaves de API e outros dados não confidenciais. Os ConfigMaps são usados para separar a configuração dos aplicativos de sua imagem de contêiner e permitir que as configurações sejam modificadas sem a necessidade de recriar a imagem do contêiner. Os ConfigMaps também podem ser usados em diferentes formatos, como texto simples ou arquivos.


### Init container

init container é um tipo especial de contêiner que é executado antes do contêiner principal de um pod. A principal função de um init container é realizar tarefas de inicialização que precisam ser concluídas antes que o contêiner principal possa ser executado.

Alguns exemplos comuns de tarefas de inicialização incluem:

Inicialização de um banco de dados ou outros recursos de armazenamento de dados
Configuração de credenciais de autenticação ou autorização
Realização de tarefas de pré-processamento de dados
Verificação da disponibilidade de recursos externos
O init container é executado em seu próprio espaço de contêiner, mas pode compartilhar volumes com o contêiner principal do pod, permitindo que ele acesse dados ou arquivos necessários para concluir sua tarefa de inicialização. Quando o init container é concluído com êxito, o contêiner principal do pod é iniciado.

Os init containers são uma maneira conveniente de garantir que os pré-requisitos de um aplicativo sejam atendidos antes de iniciar o contêiner principal. Isso pode ajudar a reduzir erros de inicialização e a tornar o ambiente mais previsível e confiável.

### RBAC

RBAC é uma sigla para Role-Based Access Control (Controle de Acesso Baseado em Funções). É um modelo de segurança usado em sistemas de TI que controla o acesso a recursos baseado em papéis ou funções atribuídas a usuários ou grupos.

No contexto do Kubernetes, o RBAC é um mecanismo de segurança que permite definir permissões granulares para usuários e grupos que acessam os recursos do cluster. Isso permite que os administradores de cluster atribuam funções e permissões específicas para diferentes usuários, garantindo que apenas aqueles com permissão possam acessar determinados recursos.

No Kubernetes, as permissões RBAC são definidas em dois objetos principais: Role e RoleBinding. Um Role define as permissões para um conjunto específico de recursos em um determinado namespace, enquanto um RoleBinding associa um Role a um usuário ou grupo específico.

O RBAC é importante para garantir a segurança e o controle de acesso em um ambiente de Kubernetes. Ele permite que os administradores de cluster gerenciem com precisão as permissões de acesso dos usuários, evitando que usuários não autorizados tenham acesso a recursos críticos ou comprometam a segurança do cluster.

Em resumo, o RBAC é um modelo de segurança baseado em funções que permite definir permissões granulares para usuários e grupos que acessam os recursos do cluster Kubernetes. Isso ajuda a garantir a segurança e o controle de acesso em um ambiente de Kubernetes.

### Ingress

ingress é um objeto que gerencia o acesso externo aos serviços em um cluster. Ele age como um controlador de tráfego que expõe rotas HTTP e HTTPS para serviços dentro do cluster. O ingress geralmente é configurado para redirecionar solicitações com base em regras definidas pelo usuário para serviços específicos, com base em nomes de host e caminhos de URL.

O ingress é usado para rotear o tráfego externo para dentro do cluster e para permitir que os serviços dentro do cluster sejam acessados de fora. O ingress também pode ser configurado para fornecer balanceamento de carga entre vários pods que executam o mesmo serviço.

O ingress é implementado por meio de um controlador de ingress, que pode ser instalado separadamente ou como parte de uma solução de plataforma mais ampla, como o Kubernetes Engine do Google Cloud Platform. Existem vários controladores de ingress disponíveis, cada um com suas próprias configurações e recursos, incluindo o Nginx ingress controller, o Traefik ingress controller e o Istio ingress gateway.
