# Desafio Day 14 - Autoscaling com giropops-senha e locust

Neste desafio vamos montar um cluster no  [MeuHomeLab](https://github.com/Carlos-Claro/descomplicando-kubernetes/tree/2436de63878ff03037c5c12ef2e4e84b4bdea581/labs), utilizando o a imagem do linuxtips/giropops-senhas que melhoramos com chainguard e está alocado em [carlosclaro/goropops-senhas](https://hub.docker.com/repository/docker/carlosclaro/goropops-senhas/general).\
Iremos realizar o teste de stress com [Locust](https://docs.locust.io/en/stable).\
Para isso iremos expor os componentes nos seguintes endereços:
- [giropops-senhas](https://giropops-senhas.carlosclaro.com.br) 
- [locust](https://locust.carlosclaro.com.br)

Para isso, utilizamos os seguintes arquivos:
- [giropops-senhas-deployment.yaml](./giropops-senhas-deployment.yaml) - Arquivo de montagem da imagem melhorada com chainguard do giropops-senhas, e do redis também do chainguard. Já com seus limites.
- [giropops-senhas-service.yaml](./giropops-senhas-service.yaml) - expose os serviços de giropops-senhas e redis, para a aplicação utilizar
- [giropops-senhas-ingress.yaml](./giropops-senhas-ingress.yaml) - expose a aplicação giropops-senhas no endereço [https://giropops-senhas.carlosclaro.com.br](giropops-senhas.carlosclaro.com.br), utilizando certificado secretName: giropops-senhas-carlosclaro-tls-prod
- [giropops-senhas-hpa.yaml](./giropops-senhas-hpa.yaml) - aplica as regras de - Resource de CPU e Memoria tanto para o redis como para o giropops-senhas
- [locust-configmap.yaml](./locust-configmap.yaml) - define as tasks que vão ser aplicadas, tanto de lista de senhas como gerar senha
- [locust-deployment.yaml](./locust-deployment.yaml) - cria a aplicação de locust com volume e aplicação Python
- [locust-service.yaml](./locust-service.yaml) - expose do serviço com uso de basic-auth para login. 
- [locust-ingress.yaml](./locust-ingress.yaml) - expose da aplicação para o endereço [locust.carlosclaro.com.br](https://locust.carlosclaro.com.br)

## Execução e métricas

Após aplicar todos arquivos acima o resultado é este:
```
> k get pods                                                      kubernetes-admin@kubernetes
NAME                                READY   STATUS    RESTARTS      AGE
giropops-senhas-7fbb5f8769-npwrc    1/1     Running   0             28s
giropops-senhas-7fbb5f8769-qgjpm    1/1     Running   0             36m
giropops-senhas-7fbb5f8769-t7m95    1/1     Running   0             13s
locust-giropops-847c48b467-69ppv    1/1     Running   3 (63m ago)   63m
redis-deployment-77b6df7bbf-gqpkz   1/1     Running   0             17m

> k get svc                                                       kubernetes-admin@kubernetes
NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
giropops-senhas   ClusterIP   10.99.184.34     <none>        80/TCP     61m
kubernetes        ClusterIP   10.96.0.1        <none>        443/TCP    3d
locust-giropops   ClusterIP   10.99.182.111    <none>        80/TCP     57m
nginx             ClusterIP   10.107.226.57    <none>        80/TCP     22h
redis-service     ClusterIP   10.111.151.164   <none>        6379/TCP   61m
> k get hpa                                                       kubernetes-admin@kubernetes
NAME                  REFERENCE                     TARGETS                         MINPODS   MAXPODS   REPLICAS   AGE
giropops-senhas-hpa   Deployment/giropops-senhas    cpu: 70%/50%, memory: 34%/50%   3         10        3          65m
redis-hpa             Deployment/redis-deployment   cpu: 20%/50%, memory: 10%/50%   1         10        1          65m

```
A aplicação está acessivel em [giropops.carlosclaro.com.br](giropops.carlosclaro.com.br).\
Acessando [locust.carlosclaro.com.br](locust.carlosclaro.com.br) podemos definir os testes e logo abaixo veremos o resultado:
```


```