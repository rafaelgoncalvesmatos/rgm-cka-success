1. Criar um NodePort Service

Crie um service do tipo NodePort chamado nodeport-svc que expõe o deployment web-deploy na porta 8080 e deve ser acessível na porta 30080 no host.

````
kubectl expose deployment web-deploy --name=nodeport-svc --type=NodePort --port=8080 --node-port=30080
````

2. Criar um Secret e montá-lo em um Pod

Crie um Secret chamado db-secret com as chaves username e password (valores fictícios: admin e admin123). Em seguida, crie um Pod que monte esse Secret no diretório /etc/secret dentro do Pod.

````
kubectl create secret generic db-secret --from-literal=username=admin --from-literal=password=admin123
````

````
apiVersion: v1
kind: Pod
metadata:
  name: secret-pod
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret"
  volumes:
  - name: secret-volume
    secret:
      secretName: db-secret

````

Aplicando:

````
kubectl apply -f pod-with-secret.yaml
````

3. Aplicar uma política de rede (Network Policy)

Crie uma NetworkPolicy chamada allow-web no namespace dev que permita apenas o tráfego de ingress (entrada) de Pods com o label app=frontend para o Pod web-deploy.

````
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-web
  namespace: dev
spec:
  podSelector:
    matchLabels:
      app: web-deploy
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend

````

Aplicando:

````
kubectl apply -f networkpolicy.yaml

````

4. Criar um DaemonSet

Crie um DaemonSet chamado log-agent usando a imagem fluentd. Ele deve rodar em todos os nodes do cluster.

````
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: log-agent
spec:
  selector:
    matchLabels:
      app: log-agent
  template:
    metadata:
      labels:
        app: log-agent
    spec:
      containers:
      - name: fluentd
        image: fluentd

````

````
kubectl apply -f daemonset.yaml

````

5. Atualizar um Deployment com Zero Downtime

Atualize o Deployment web-deploy para usar a imagem nginx:1.19 sem causar downtime.

````
kubectl set image deployment/web-deploy nginx=nginx:1.19
````

6. Configurar RBAC (Role-Based Access Control)

Crie uma Role chamada pod-reader no namespace prod que permita listar, ver e deletar Pods. Em seguida, crie um RoleBinding chamado dev-binding para o usuário dev-user, permitindo-o usar essa Role.

Criar a role:
````
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: prod
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "delete"]

````

````
kubectl apply -f role.yaml

````

Criar o RoleBinding:

````
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-binding
  namespace: prod
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io

````

````
kubectl apply -f rolebinding.yaml

````

7. Corrigir um Pod em CrashLoopBackOff

Você tem um Pod chamado crashing-pod que está em estado CrashLoopBackOff. Como você descobriria a causa do problema e tentaria corrigir?

Verificando Logs:
````
kubectl logs crashing-pod
````

Descrever o Pod para verificar eventos:
````
kubectl describe pod crashing-pod

````

8. Configurar um Ingress Resource

Crie um recurso Ingress chamado web-ingress que roteie requisições para o serviço web-deploy no path /web.

````
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-deploy
            port:
              number: 80

````
kubectl apply -f ingress.yaml

````

9. Criar um Job no Kubernetes

Crie um Job chamado backup-job que execute o comando echo "Backup done" usando a imagem busybox.

````
apiVersion: batch/v1
kind: Job
metadata:
  name: backup-job
spec:
  template:
    spec:
      containers:
      - name: backup
        image: busybox
        command: ["echo", "Backup done"]
      restartPolicy: Never
  backoffLimit: 4

````

````
kubectl apply -f job.yaml

````
10. Criar um AutoScaler

Configure um Horizontal Pod Autoscaler (HPA) para o deployment web-deploy, escalando-o automaticamente entre 2 e 10 réplicas quando a utilização de CPU exceder 80%.

````
kubectl autoscale deployment web-deploy --min=2 --max=10 --cpu-percent=80

````