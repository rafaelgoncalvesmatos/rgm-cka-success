

1. Backup e Restore do ETCD
Como você faria o backup de um cluster ETCD e depois restauraria o backup para um cluster?

````
ETCDCTL_API=3 etcdctl snapshot save /tmp/etcd-backup.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
````

Restaurar

````
ETCDCTL_API=3 etcdctl snapshot restore /tmp/etcd-backup.db \
  --data-dir=/var/lib/etcd-from-backup \
  --initial-cluster=master=https://127.0.0.1:2380 \
  --initial-advertise-peer-urls=https://127.0.0.1:2380 \
  --name=master
````

Atualizar o arquivo de configuração do ETCD com o novo data-dir.

2. Configurar um Pod com Tolerations e Node Affinity
Crie um Pod chamado restricted-pod que só pode rodar em nodes com a label env=production e que tolere o taint key=dedicated:NoSchedule.

````
apiVersion: v1
kind: Pod
metadata:
  name: restricted-pod
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "NoSchedule"
    effect: "NoSchedule"
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: "env"
            operator: In
            values:
            - production
````

````
kubectl apply -f pod-with-affinity-toleration.yaml
````

3. Configurar Limites de Recursos em um Namespace
Crie um ResourceQuota no namespace dev chamado dev-quota que limite o uso de CPU a 4 cores e memória a 8Gi para todos os Pods.

````
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-quota
  namespace: dev
spec:
  hard:
    requests.cpu: "4"
    requests.memory: 8Gi
````

````
kubectl apply -f resourcequota.yaml
````

4. Criar um StatefulSet
Crie um StatefulSet chamado db-statefulset usando a imagem postgres:12. O StatefulSet deve garantir que os Pods sejam nomeados sequencialmente e mantenham seu estado após reinicializações.

````
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: db-statefulset
spec:
  serviceName: "db"
  replicas: 3
  selector:
    matchLabels:
      app: db
  template:
    metadata:
      labels:
        app: db
    spec:
      containers:
      - name: postgres
        image: postgres:12
        volumeMounts:
        - name: postgres-pv
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: postgres-pv
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 5Gi
````

````
kubectl apply -f statefulset.yaml
````

5. Configurar um Persistent Volume (PV) e um Persistent Volume Claim (PVC)
Crie um PV chamado app-pv de 5Gi e um PVC chamado app-pvc que use o PV, para armazenar dados persistentes de um Pod chamado app-pod.

PV YAML:
````
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
````

PVC YAML:
````
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
````

Pod YAML:
````
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: app-storage
  volumes:
  - name: app-storage
    persistentVolumeClaim:
      claimName: app-pvc
````

Aplicando:
````
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
kubectl apply -f pod-with-pvc.yaml
````

6. Usar ServiceAccount para segurança
Crie um ServiceAccount chamado secure-sa no namespace prod. Crie um Pod que use essa ServiceAccount para acessar recursos de forma segura.

````
kubectl create serviceaccount secure-sa --namespace prod
````

````
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
  namespace: prod
spec:
  serviceAccountName: secure-sa
  containers:
  - name: nginx
    image: nginx
````

````
kubectl apply -f pod-with-serviceaccount.yaml
````


7. Configurar um Pod Security Policy (PSP)
Crie uma Pod Security Policy chamada restricted-psp que limite a criação de Pods que rodem como root e que permitam o uso de volumes emptyDir.

````
kubectl create serviceaccount secure-sa --namespace prod
````

Criar o Pod com ServiceAccount:
````
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
  namespace: prod
spec:
  serviceAccountName: secure-sa
  containers:
  - name: nginx
    image: nginx
````

````
kubectl apply -f podsecuritypolicy.yaml
````

8. Criar um ClusterRole e ClusterRoleBinding
Crie um ClusterRole chamado admin-role que permita listar, criar, deletar e atualizar qualquer recurso dentro do cluster. Depois, crie um ClusterRoleBinding chamado admin-binding para aplicar essa ClusterRole ao usuário cluster-admin.

Criar Cluster Role:

````
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: admin-role
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["get", "list", "create", "delete", "update"]
````

Criar o Cluster Role Binding:

````
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-binding
subjects:
- kind: User
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: admin-role
  apiGroup: rbac.authorization.k8s.io
````

Aplicando YAML:

````
kubectl apply -f clusterrole.yaml
kubectl apply -f clusterrolebinding.yaml
````

9. Habilitar Network Policy para isolar tráfego
Implemente uma NetworkPolicy no namespace prod chamada deny-all que bloqueie todo o tráfego de e para os Pods, exceto o tráfego permitido explicitamente.

Network Policy YAML:

````
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: prod
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
````

````
kubectl apply -f networkpolicy-deny-all.yaml
````

10. Gerenciar Namespaces
Crie um namespace chamado test-env e mova o Pod test-pod para esse namespace sem recriá-lo (use a ferramenta kubectl para isso).

Essas questões cobrem tópicos como backup de ETCD, gerenciamento avançado de Pods e volumes, segurança com ServiceAccounts, ClusterRole, e Network Policies, que também são exigidos no exame CKA.

Criar Namespace:

````
kubectl create namespace test-env
````

Mover o Pod para o namespace (recriacao):

````
kubectl get pod test-pod -o yaml --export > pod.yaml
kubectl delete pod test-pod
kubectl apply -f pod.yaml --namespace=test-env
````