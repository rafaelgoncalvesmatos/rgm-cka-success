Vamos lá! Aqui estão algumas questões práticas para testar seu conhecimento atual na preparação para a CKA. Tente resolver e, se precisar de ajuda, posso te orientar.

1. Criar um Pod simples

Crie um pod chamado my-nginx com a imagem nginx:latest.

``` 
❯ kubectl run my-nginx --image=nginx:latest
pod/my-nginx created
❯ kubectl get pods
NAME                                                   READY   STATUS              RESTARTS      AGE
my-nginx                                               0/1     ContainerCreating   0             6s
my-prometheus-alertmanager-0                           1/1     Running             1 (27s ago)   9d
my-prometheus-kube-state-metrics-85449f8874-m9s45      0/1     Running             1 (27s ago)   9d
my-prometheus-prometheus-node-exporter-96bsv           1/1     Running             1 (27s ago)   9d
my-prometheus-prometheus-node-exporter-sqhh2           1/1     Running             1 (27s ago)   9d
my-prometheus-prometheus-pushgateway-64f948f86-rlps6   0/1     Running             1 (27s ago)   9d
my-prometheus-server-7484cb7db5-9wmhd                  1/2     Running             2 (27s ago)   9d
``` 

2. Verificar logs de um Pod

O Pod my-nginx está rodando, mas os logs indicam um comportamento estranho. Como você verificaria os logs desse Pod?

```
❯ kubectl logs my-nginx  -f
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/09/08 23:08:46 [notice] 1#1: using the "epoll" event method
2024/09/08 23:08:46 [notice] 1#1: nginx/1.27.1
2024/09/08 23:08:46 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
```


3. Criar um Deployment

Crie um deployment chamado web-deploy que use a imagem nginx:1.18. Ele deve ter 3 réplicas.

````
kubectl create deployment web-deploy --image=nginx:1.18 --replicas=3
````


4. Escalar um Deployment

Agora, escale o web-deploy para 5 réplicas.

5. Configurar um Service

Crie um service chamado web-svc do tipo ClusterIP para expor o deployment web-deploy na porta 80.

6. Acessar um Pod em um Namespace específico

Você tem um Pod chamado my-app rodando no namespace dev. Como você acessaria o terminal desse Pod?

7. Configurar um ConfigMap

Crie um ConfigMap chamado app-config com uma chave APP_MODE definida como production.

8. Montar um Volume de ConfigMap em um Pod

Agora, crie um Pod que usa o ConfigMap app-config, montando a chave APP_MODE em um arquivo no diretório /etc/config.

9. Criar um Persistent Volume Claim (PVC)

Crie um Persistent Volume Claim (PVC) chamado data-pvc de 1Gi no namespace prod, usando a classe de armazenamento standard.

10. Restringir o acesso ao namespace

Implemente uma Role no namespace dev que permita apenas que os usuários listem Pods.

Essas questões cobrem várias áreas da CKA.