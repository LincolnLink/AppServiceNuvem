# AppServiceNuvem

# Criando um container sem o dockerfile

cd ./dotnet-docker 
 dotnet publish /p:PublishProfile=DefaultContainer

# Implantar um microsserviço .NET no Kubernetes

 - O microsserviços possibilita escalar horizontalmente aplicativos, e atende os numeros de container que é criado, seja para aumentar ou reduzir o numero, em algumas projetos complexos, implantar, atualizar, monitorar, remover containeres, pode ser uma dificuldade.

 - O Kubernetes ele resolve essa dificuldade, ele trabalha como orquestração, ele é parecido com as plataforma de serviço (Paas) ou infraestrutura como serviço (IaaS).

 - 