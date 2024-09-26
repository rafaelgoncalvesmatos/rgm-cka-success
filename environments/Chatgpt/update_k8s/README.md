# Atualização do K8s

## Passos preliminares

Antes de atualizar o cluster, verifique a versao atual do cluster e faça um backup do etcd.

1. Verifique a versão atual do cluster
````
kubectl version --short
````
2. Backuo do etcd: faça um backup do etcd, pois ele contem dados do estado do cluster:
````
ETCDCTL_API=3 etcdctl snapshot save snapshot.db \
--endpoints=https://localhost:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key
````
3. Verificar a compatibilidade da nova versao: Antes de atualizar, consulte as notas de versao e verifique a compatibilidade de upgrades. Por exemplo a versao do destino deve ser no maximo uma versao minor acima da atual.

## Parte 1: Atualizacao dos nos de controle ( Master Nodes )

A atualizacao dos master nodes deve ser feita um nó de cada vez para evitar interrepcoes no cluster.

1. Atualizar o kubeadm no nó master
Primeiro, atualize o pacote "kubeadm" no primeiro nó de controle.
````
sudo apt update
sudo apt install -y kubeadm=<nova-versao>
````

Verifique a nova versao instalada:
````
kubeadm version
````

2. Atualizar o cluster com kubeadm

Atualize o cluster executando o comando de upgrade no nó master:
````
sudo kubeadm upgrade plan
sudo kubeadm upgrade apply v<nova-versao>
````

Isso atualizara os componentes principais do Kubernetes, como etcd, kube-apiserver, kube-controller-manager e kube-scheduler.

3. Atualizar o kubelet e reiniciar o serviço

Apos a atualizacao do kubeadm, voce tambem precisa atualizar "kubelet" e reinicia-lo.
````
sudo apt install -y kubelet=<nova-versao>
sudo systemctl daemon-reload
sudo systemctl restart kubelet
````

4. Repetir o processo nos outros nós de controle

Depois de concluir o processo no primeiro nó master, repita os passos 1 a 3 para os outros dois nós de controle um de cada vez.

## Parte 2: Atualizacao dos Nós de Trabalho ( Worker Nodes )

Depois de atualizar todos os nós de controle, voce pode proceder com a atualizacao dos nós de trabalho, tambem um nó de cada vez.

1. Drenar o nó de trabalho

Antes de atualizar o nó de trabalho, drene-o para que nenhum pod critico seja executado durante o processo.
````
kubectl drain <node-name> --ignore-daemonsets --delete-local-data
````

2. Atualizar o kubeadm no nó de trabalho

Instale a nova versao do kubeadm no nó de trabalho:
````
sudo apt update 
sudo apt install -y kubeadm=<nova-versao>
````

Verique a nova versao instalada:
````
kubeadm version
````

3. Atualizar o nó de trabalho com kubeadm

Em seguinda, execute o comando para atualizar o nó de trabalho:
````
sudo kubeadm upgrade node
````

4. Atualizar o kubelet e reiniciar o servico

Como nos nós de controle, voce tambem precisa atualizar o "kubelet" e reinicia-lo:
````
sudo apt install -y kubelet=<nova-versao>
sudo systemctl daemon-reload
sudo systemctl restart kubelet
````

5. Reativar o nó de trabalho

Apos atualizar o nó, reative-o no cluster:
````
kubectl uncordon <node-name>
````

6. Repetir o processo para o outro nó de trabalho

Agora, repita os passos 1 a 5 para o segundo nó de trabalho.

## Parte 3: Verificacoes pos-atualizacao

Após a atualizacao de todos os nós, verifique o status do cluster para garantir que todos os nós estejam funcionando corretamente.

1. Verificar o status do nós:
````
kubectl get nodes
````

2. Verificar os pods em todos os namespaces:
````
kubectl get pods --all-namespaces
````

3. Verificar a versao do cluster:
````
kubectl version --short
````

Se todos os nós estiverem "Ready" e a versão estiver correta, a atualização foi concluida com sucesso.

Conclusão:
* Certifique-se de monitorar o cluster após a atualização para detectar qualquer comportamento anomolo.
* O tempo de atualização depende da infraestrutura e do numero de pods em execucao, entao monitore as metricas do cluster e ajuste a operacao conforme necessario.