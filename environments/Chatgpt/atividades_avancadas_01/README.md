# CKA Certificação - Atividade Avançadas 01

## 1. Gerenciamento de Certificado

Voce precisa gerar um certificado TLS para um serviço que será exposto via Ingress. O certificado deve ser gerado usando OpenSSL e aplicado ao cluster Kubernetes.

Passos:
1. Gerar o certificado TLS usando OpenSSL.
2. Criar um Secret para armazenar o certificado.
3. Configurar o ingress para usar o Secret com TLS.

Comandos:

* Gerar o certificado e a chave privada:

````
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=my-app.example.com/O=my-app"
````

* Criar um Secret com o certificado e a chave:

````
kubectl create secret tls my-app-tls --cert=tls.crt --key=tls.key
````

* Configurar o Ingress com o TLS:

````yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
    name: my-app-ingress
spec:
    tls:
    - hosts:
        - "my-app.example.com"
      secretName: my-app-tls
    rules:
    - host: "my-app.example.com"
      http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
                name: my-app-service
                port:
                    number: 80
````

Aplicar o Ingress:

````
kubectl apply -f ingress-tls.yaml
````

## 2. Configuração de RBAC ( Role-Based Access Control )

Voce precisa criar uma politica RBAC que de permissao a um usuario especifico para apenas listar os Pods em um namespace especifico.

Passos:
1. Criar um Role que permite listar Pods.
2. Criar um RoleBinding para associar a Role ao usuario.

Comandos:

* Criar a Role:

````yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
    namespace: my-namespace
    name: pod-lister
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
````

* Criar o RoleBinding:

````yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
    name: list-pods-binding
    namespace: my-namespace
subjects:
- kind: User
  name: my-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-lister
  apiGroup: rbac.authorization.k8s.io
````

Aplicar os YAMLs:

````
kubectl apply -f role.yaml
kubectl apply -f rolebinding.yaml
````

## 3. Configurar Cluster Autoscaler

Voce precisa configurar um Cluster Autoscaler para que o cluster Kubernetes adicione automaticamente mais nós quando necessario.

Passos:

1. instalar e configurar o Cluster Autoscaler.
2. Configurar a politica de escalonamento automatico.

Comandos:
* Criar o deployment do cluster Autoscaler:
````yaml
apiVersion: app/v1
kind: Deployment
metadata: 
    name: cluster-autoscaler
    namespace: kube-system
spec:
    replicas: 1
    selector:
        matchLabels:
            app: cluster-autoscaler
    template:
        metadata:
            labels:
                app: cluster-autoscaler
        spec:
            containers:
            - image: k8s.gcr.io/autoscaling/cluster-autoscaler:v1.18.1
              name: cluster-autoscaler
              command:
              - ./cluster-autoscaler
              - --v=4
              - --cloud-provider=<cloud-provider>
              - --nodes=1:1:<node-group-name>
````

Aplicar o deployment:

````
kubectl apply -f cluster-autoscaler.yaml
````

* Configurar as politicas de escalonamento automatico ( maximo e minimo de nos ) de acordo com as suas necessidades.

## 4. Diagnostico de Problemas com Volumes Persistentes

Um Pod está falhando ao montar um Persistent Volume Claim ( PVC ). Voce precisa diagnosticar e resolver o problema.

Passos:
1. Verificar o status do PVC e PV.
2. Verificar os eventos associados ao Pod e PVC.
3. Resolver o problema e aplicar novamente.

Comandos:

* Verificar o status do PVC:

````
kubectl get pvc
````

* Descrever o PVC para mais detalhes:

````
kubectl describe pvc <pvc-name>
````

* Verificar eventos do Pod que está usando o PVC:

````
kubectl describe pod <pod-name>
````

* Verificar o status do Persistent Volume:

````
kubectl get pv
````

## 5. Trabalhar com Secrets

Voce precisa armazenar uma senha sensivel como um Secret e monta-la como uma variavel de ambiente dentro de um Pod.

Passos: 
1. Criar o Secret.
2. Criar um Pod que usa o Secret como variavel de ambiente.

Comandos:
* Criar um Secret

````
kubectl create secret generic db-password --from-literal=password="minhasenha"
````

* Criar o Pod que usa o Secret:

````yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: my-container
    image: nginx
    env:
    - name: DB_PASSWORD
      volueFrom:
        secretKeyRef:
          name: db-password
          key: password
````

Aplicar o Pod:

````
kubectl apply -f secret-env-pod.yaml
````

## 6. Gerenciamento de Labels e Selectors

Voce precisa aplicar labels aos Pods existentes e configurar um Service para selecionar Pods baseados nessas labels.

Passos.

1. Aplicar labels a Pods existentes.
2. Criar um Service que seleciona Pods com a label aplicada.

Comandos:

* Aplicar uma label a um Pod:

````
kubectl label pod <pod-name> app=my-app
````

* Criar o Service que seleciona Pods com a Label:

````yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort:8080
````

Aplicar o Service:

````
kubectl apply -f service.yaml
````

## 7. Criar um Deployment com Estrategia de Rolling Update

Voce precisa configurar um Deployment que atualiza os Pods de forma gradual para evitar downtime, usando rolling update.

Passos: 
1. Criar o Deployment com estrategia de rolling update.

Comandos:

````yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-deployment
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
    selector:
      matchLabels:
        app: my-app
    template:
      metadata:
        labels:
          app: my-app
      spec:
        containers:
        - name: my-container
          image: nginx:1.14
````

Aplicar o Deployment:

````
kubectl apply -f rolling-deployment.yaml
````

## 8. Backup e Restore de etcd

Voce precisa realizar o backup e restaurar após uma falha.

Passos:

1. Realizar o backup do etcd.
2. Restaurar o backup em caso de falha.

Comandos:
* Realizar o backup do etcd:

````sh
ETCDCTL_API=3 etcdctl snapshot save snapshot.db \
--endpoints=https://localhost:2379 \
--cacerts=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key
````

* Restaurar o backup:

````sh
ETCDCTL_API=3 etcdctl snapshot restore snapshot.db \
--endpoints=https://localhost:2379 \
--cacerts=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key
````
