# AppServiceNuvem

# Criando um container sem o dockerfile(a forma mais rapida)

 - dotnet publish /p:PublishProfile=DefaultContainer

 - docker compose up

# Implantar um microsserviço .NET no Kubernetes

 - O microsserviços possibilita escalar horizontalmente aplicativos, e atende os numeros de container que é criado, seja para aumentar ou reduzir o numero, em algumas projetos complexos, implantar, atualizar, monitorar, remover containeres, pode ser uma dificuldade.

 - O Kubernetes ele resolve essa dificuldade, ele trabalha como orquestração, ele é parecido com as plataforma de serviço (Paas) ou infraestrutura como serviço (IaaS).

# Usando dev container, é possivel debugar container (estudo futuro)

 - >Dev Containers: Rebuild and Reopen in Container

# Fazer upload das imagens para o Docker Hub

docker tag store lincolnlink/storeimage
docker tag products lincolnlink/productservice

docker push lincolnlink/storeimage
docker push lincolnlink/productservice

# Implantar um contêiner de microsserviço no Kubernetes

 ### Instalar ferramentas e implementação do Kubernetes

 - Antes de instalar deve executar o comando "wsl" para iniciar uma sessão Ubunto.
 - Verifica se o Ubnto está instalado, com o comando: "wsl --list --verbose"

 - Comando para instalar instalar os pré-requisitos:

 sudo apt-get update
 sudo apt-get install -y apt-transport-https ca-certificates curl

 - Em seguida, para baixar a chave de assinatura para os repositórios 
 de pacote do Kubernetes, execute estes comandos:

 sudo mkdir /etc/apt/keyrings

 curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

 - Adicione o repositório do Kubernetes à configuração do apt:

 echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

 - Agora, você pode instalar a ferramenta kubectl:

sudo apt-get update
sudo apt-get install -y kubectl

 - Por fim, instale a implementação k3d do Kubernetes e crie um cluster:

curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash

k3d cluster create devcluster --config k3d.yml


 - Cria um arquivo chamado "back-end-deploy.yml" com a configuração para buscar a imagem
no github e criar o cluster para rodar o container.

 - Comando e executar o microsserviço de back-end para implementar : 

kubectl apply -f backend-deploy.yml

 - Executa o comando para listar o container do kubernet

kubectl get pods

 - Resultado : 

paladino@DESKTOP-8TA92CI:/mnt/p/Projetos e Estudos/AppServiceNuvem/mslearn-dotnet-cloudnative/dotnet-kubernetes$ kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
productsbackend-544d68fb94-xshkq   1/1     Running   0          62s

 - O comando kubectl apply retorna rapidamente. Mas a criação do contêiner pode demorar um pouco. Para exibir o progresso, use o código a seguir.

 - Para consultar alguns produtos, acrescente o endereço com /api/product e pressione Enter. Você deve ver algumas informações do produto listadas no formato JSON.

https://2b71mjqh-32001.brs.devtunnels.ms/api/product

 - Crie um novo arquivo na pasta donet-kubernetes chamado frontend-deploy.yml 

 - Observe que esse arquivo é semelhante ao que criamos para o microsserviço de back-end. Há duas diferenças:

 - Estamos especificando um contêiner diferente para ser executado no valor spec.template.spec.containers.image da implantação.

 - Há uma nova variável de ambiente na seção spec.template.spec.containers.env. O código no aplicativo storefrontend chama o back-end, mas como não especificamos um nome de domínio totalmente qualificado (FQDN), não sabemos o endereço IP do microsserviço de back-end, usamos o nome especificado no nó metadata.name do Deployment. O Kubernetes cuida do resto.

 - Implante o contêiner no Kubernetes com o seguinte comando

 kubectl apply -f frontend-deploy.yml

 Use kubectl get pods para ver o status da implantação. Depois que a linha do storefrontend exibir Em execução na coluna STATUS, tudo estará pronto.

 Neste exercício, você criou um arquivo de implantação descrevendo exatamente como queria que os contêineres fossem executados no Kubernetes. Você fez com que o Kubernetes baixasse a imagem do Docker Hub e inicializasse os contêineres.

 # Dimensionar uma instância de contêiner no Kubernetes

  - executa o comando "wsl" poder digitar os comando do kubernetes, vc precisa entrar no linux para usar o kubernetes.

  - No codespace, na guia TERMINAL, execute o seguinte comando para dimensionar o microsserviço de back-end para cinco instâncias:

    kubectl scale --replicas=5 deployment/productsbackend

  - Para verificar se as cinco instâncias estão funcionando, execute este comando:

    kubectl get pods

  - Para reduzir a instância, execute o seguinte comando:

    kubectl scale --replicas=1 deployment/productsbackend

  - RESULTADO:

    NAME                               READY   STATUS    RESTARTS   AGE
    productsbackend-544d68fb94-6ckqn   1/1     Running   0          3s 
    productsbackend-544d68fb94-bs2bf   1/1     Running   0          3s
    productsbackend-544d68fb94-p4l6b   1/1     Running   0          2d18h
    productsbackend-544d68fb94-snkgg   1/1     Running   0          3s
    productsbackend-544d68fb94-z9cqm   1/1     Running   0          3s
    storefrontend-5b595dc9fc-dmw6w     1/1     Running   0          2d18h 

# Comprovar a resiliência do microsserviço no Kubernetes

 - Um dos benefícios do Kubernetes é o suporte para o gerenciamento de configuração declarativo. Os serviços que você definir nos arquivos de configuração são mantidos a qualquer custo.

 - Isso significa que, se houver uma falha, o Kubernetes reiniciará automaticamente os serviços que estavam em execução antes da falha.

 - Vejamos essa resiliência em ação excluindo o pod storefrontend e verificando se o Kubernetes o reiniciou.

 - 1 - Primeiro, no TERMINAL no codespace, execute kubectl get pods e anote o nome, incluindo a cadeia de caracteres aleatória, do pod storefrontend. Veja um exemplo de saída:

 NAME                               READY   STATUS    RESTARTS   AGE
 productsbackend-544d68fb94-p4l6b   1/1     Running   0          2d18h
 storefrontend-5b595dc9fc-dmw6w     1/1     Running   0          2d18h

 - 2 - Agora, exclua o pod storefrontend usando o comando kubectl delete. Você precisa especificar o nome completo do pod, incluindo a cadeia de caracteres aleatória.

 kubectl delete pod storefrontend-5b595dc9fc-dmw6w

 - 3 - Como o Kubernetes mantém o estado do sistema conforme declarado nos arquivos de configuração, ele inicia imediatamente outra instância do pod. Verifique isso executando kubectl get pods.

 NAME                               READY   STATUS    RESTARTS   AGE
 productsbackend-544d68fb94-p4l6b   1/1     Running   0          2d18h
 storefrontend-5b595dc9fc-cmxl6     1/1     Running   0          3s

 - Observe que a cadeia de caracteres aleatória após o nome storefrontend é alterada, indicando que o pod é uma nova instância. Além disso, o valor de AGE também é consideravelmente menor.

 























 





