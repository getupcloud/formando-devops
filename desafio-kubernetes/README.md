# Estamos preparando o desafio de Docker, ele entrará aqui, mas se quiserem, podem esquentar com as questões de kubernetes

# Desafio  Docker/Kubernetes

As questões abaixo devem ser respondidas no arquivo [RESPOSTAS-kube.md](RESPOSTAS-kube.md) em um fork desse repositório. Por ordem e numeradamente. O formato é livre, mas recomanda-se as linhas de comando utilizadas para criar ou alterar resources de cada questão na maioria dos casos. Quanto mais sucinto e direto, melhor. Envie o endereço do seu repositório para desafio@getupcloud.com.

Ex. de respostas:

### 1 - linha de comando para criar um resource x

```bash
    kubectl cria um resource x
```

ou

### 2 - crie um resource y

```yaml
    apiVersion: xyz/v1
    Kind: XYZ
    metadata:
      name: y
      namespace: x
    spec:
    ....
```

---
---

## Caso precise de um kubernetes, segue uma dica rápida para a criação de um cluster kubernetes kind, no seu linux ou mac, ja com docker instalado.

1 - tenha certeza que ja tenha docker instalado na sua maquina

2 - baixe e instale o kind 

```bash
# Linux
sudo curl -Lo /usr/local/bin/kind https://github.com/kubernetes-sigs/kind/releases/download/v0.16.0/kind-linux-amd64
sudo chmod +x /usr/local/bin/kind
# MacOS
sudo curl -Lo /usr/local/bin/kind  https://github.com/kubernetes-sigs/kind/releases/download/v0.16.0/kind-darwin-arm64
sudo chmod +x /usr/local/bin/kind
```

3 - salve e use esse arquivo abaixo para subir seu cluster, ele é um exemplo de como o kind é poderoso e pode te ajudar a subir um kubernetes rapido e facil

```bash
cat << EOF | kind create cluster --name meuk8s --config -
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
EOF
```

---
---

## Bora começar !!!

1 - com uma unica linha de comando capture somente linhas que contenham "erro" do log do pod `serverweb` no namespace `meusite` que tenha a label `app: ovo`.

2 - crie o manifesto de um recurso que seja executado em todos os nós do cluster com a imagem `nginx:latest` com nome `meu-spread`, nao sobreponha ou remova qualquer taint de qualquer um dos nós.

3 - crie um deploy `meu-webserver` com a imagem `nginx:latest` e um initContainer com a imagem `alpine`. O initContainer deve criar um arquivo /app/index.html, tenha o conteudo "HelloGetup" e compartilhe com o container de nginx que só poderá ser inicializado se o arquivo foi criado.

4 - crie um deploy chamado `meuweb` com a imagem `nginx:1.16` que seja executado exclusivamente no node master.

5 - com uma unica linha de comando altere a imagem desse pod `meuweb` para `nginx:1.19` e salve o comando aqui no repositorio.

6 - quais linhas de comando para instalar o ingress-nginx controller usando helm, com os seguintes parametros;

    helm repository : https://kubernetes.github.io/ingress-nginx

    values do ingress-nginx : 
    controller:
      hostPort:
        enabled: true
      service:
        type: NodePort
      updateStrategy:
        type: Recreate

7 - quais as linhas de comando para: 

    criar um deploy chamado `pombo` com a imagem de `nginx:1.11.9-alpine` com 4 réplicas;
    alterar a imagem para `nginx:1.16` e registre na annotation automaticamente;
    alterar a imagem para 1.19 e registre novamente; 
    imprimir a historia de alterações desse deploy;
    voltar para versão 1.11.9-alpine baseado no historico que voce registrou.
    criar um ingress chamado `web` para esse deploy


8 - linhas de comando para; 

    criar um deploy chamado `guardaroupa` com a imagem `redis`;
    criar um serviço do tipo ClusterIP desse redis com as devidas portas.

9 - crie um recurso para aplicação stateful com os seguintes parametros:

    - nome : meusiteset
    - imagem nginx 
    - no namespace backend
    - com 3 réplicas
    - disco de 1Gi
    - montado em /data
    - sufixo dos pvc: data


10 - crie um recurso com 2 replicas, chamado `balaclava` com a imagem `redis`, usando as labels nos pods, replicaset e deployment, `backend=balaclava` e `minhachave=semvalor` no namespace `backend`.

11 - linha de comando para listar todos os serviços do cluster do tipo `LoadBalancer` mostrando tambem `selectors`.

12 - com uma linha de comando, crie uma secret chamada `meusegredo` no namespace `segredosdesucesso` com os dados, `segredo=azul` e com o conteudo do texto abaixo.

```bash
   # cat chave-secreta
     aW5ncmVzcy1uZ2lueCAgIGluZ3Jlc3MtbmdpbngtY29udHJvbGxlciAgICAgICAgICAgICAgICAg
     ICAgICAgICAgICAgTG9hZEJhbGFuY2VyICAgMTAuMjMzLjE3Ljg0ICAgIDE5Mi4xNjguMS4zNSAg
     IDgwOjMxOTE2L1RDUCw0NDM6MzE3OTQvVENQICAgICAyM2ggICBhcHAua3ViZXJuZXRlcy5pby9j
     b21wb25lbnQ9Y29udHJvbGxlcixhcHAua3ViZXJuZXRlcy5pby9pbnN0YW5jZT1pbmdyZXNzLW5n
     aW54LGFwcC5rdWJlcm5ldGVzLmlvL25hbWU9aW5ncmVzcy1uZ
```

13 - qual a linha de comando para criar um configmap chamado `configsite` no namespace `site`. Deve conter uma entrada `index.html` que contenha seu nome.

14 - crie um recurso chamado `meudeploy`, com a imagem `nginx:latest`, que utilize a secret criada no exercicio 11 como arquivos no diretorio `/app`.

15 - crie um recurso chamado `depconfigs`, com a imagem `nginx:latest`, que utilize o configMap criado no exercicio 12 e use seu index.html como pagina principal desse recurso.

16 - crie um novo recurso chamado `meudeploy-2` com a imagem `nginx:1.16` , com a label `chaves=secretas` e que use todo conteudo da secret como variavel de ambiente criada no exercicio 11.

17 - linhas de comando que;

    crie um namespace `cabeludo`;
    um deploy chamado `cabelo` usando a imagem `nginx:latest`; 
    uma secret chamada `acesso` com as entradas `username: pavao` e `password: asabranca`;
    exponha variaveis de ambiente chamados USUARIO para username e SENHA para a password.

18 - crie um deploy `redis` usando a imagem com o mesmo nome, no namespace `cachehits` e que tenha o ponto de montagem `/data/redis` de um volume chamado `app-cache` que NÂO deverá ser persistente.

19 - com uma linha de comando escale um deploy chamado `basico` no namespace `azul` para 10 replicas.

20 - com uma linha de comando, crie um autoscale de cpu com 90% de no minimo 2 e maximo de 5 pods para o deploy `site` no namespace `frontend`.

21 - com uma linha de comando, descubra o conteudo da secret `piadas` no namespace `meussegredos` com a entrada `segredos`.

22 - marque o node o nó `k8s-worker1` do cluster para que nao aceite nenhum novo pod.

23 - esvazie totalmente e de uma unica vez esse mesmo nó com uma linha de comando.

24 - qual a maneira de garantir a criaçao de um pod ( sem usar o kubectl ou api do k8s ) em um nó especifico.

25 - criar uma serviceaccount `userx` no namespace `developer`. essa serviceaccount só pode ter permissao total sobre pods (inclusive logs) e deployments no namespace `developer`. descreva o processo para validar o acesso ao namespace do jeito que achar melhor.

26 - criar a key e certificado cliente para uma usuaria chamada `jane` e que tenha permissao somente de listar pods no namespace `frontend`. liste os comandos utilizados.

27 - qual o `kubectl get` que traz o status do scheduler, controller-manager e etcd ao mesmo tempo

