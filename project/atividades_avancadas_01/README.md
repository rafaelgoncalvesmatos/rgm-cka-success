# Avançado - Gerenciamento de Certificado

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