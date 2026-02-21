# Desafio Day 14 - Autoscaling com giropops-senha e locust

Neste desafio vamos montar um cluster no  [MeuHomeLab](https://github.com/Carlos-Claro/descomplicando-kubernetes/tree/2436de63878ff03037c5c12ef2e4e84b4bdea581/labs), utilizando o a imagem do linuxtips/giropops-senhas que melhoramos com chainguard e está alocado em [carlosclaro/goropops-senhas](https://hub.docker.com/repository/docker/carlosclaro/goropops-senhas/general).\
Iremos realizar o teste de stress com [Locust](https://docs.locust.io/en/stable).\
Para isso iremos expor os componentes nos seguintes endereços:
- [giropops-senhas](giropops-senhas.carlosclaro.com.br) 
- [locust](locust.carlosclaro.com.br)

Para isso, utilizamos os seguintes arquivos:
- [giropops-senhas-deployment.yaml](./giropops-senhas-deployment.yaml) - Arquivo de montagem da imagem melhorada com chainguard do giropops-senhas, e do redis também do chainguard. Já com seus limites.
- [giropops-senhas-service.yaml](./giropops-senhas-service.yaml) - expose os serviços de giropops-senhas e redis, para a aplicação utilizar
- [giropops-senhas-ingress.yaml](./giropops-senhas-ingress.yaml) - expose a aplicação giropops-senhas no endereço [giropops-senhas.carlosclaro.com.br](giropops-senhas.carlosclaro.com.br), utilizando certificado secretName: giropops-senhas-carlosclaro-tls-prod
- [giropops-senhas-hpa.yaml](./giropops-senhas-hpa.yaml) - aplica as regras de - Resource de CPU e Memoria tanto para o redis como para o giropops-senhas
- [locust-configmap.yaml](./locust-configmap.yaml) - define as tasks que vão ser aplicadas, tanto de lista de senhas como gerar senha
- [locust-deployment.yaml](./locust-deployment.yaml) - cria a aplicação de locust com volume e aplicação Python
- [locust-service.yaml](./locust-service.yaml) - expose do serviço com uso de basic-auth para login. 
- [locust-ingress.yaml](./locust-ingress.yaml) - expose da aplicação para o endereço [locust.carlosclaro.com.br](locust.carlosclaro.com.br)

## Execução e métricas

