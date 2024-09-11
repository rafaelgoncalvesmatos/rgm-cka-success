1. Backup e Restore do ETCD
Como você faria o backup de um cluster ETCD e depois restauraria o backup para um cluster?

2. Configurar um Pod com Tolerations e Node Affinity
Crie um Pod chamado restricted-pod que só pode rodar em nodes com a label env=production e que tolere o taint key=dedicated:NoSchedule.

3. Configurar Limites de Recursos em um Namespace
Crie um ResourceQuota no namespace dev chamado dev-quota que limite o uso de CPU a 4 cores e memória a 8Gi para todos os Pods.

4. Criar um StatefulSet
Crie um StatefulSet chamado db-statefulset usando a imagem postgres:12. O StatefulSet deve garantir que os Pods sejam nomeados sequencialmente e mantenham seu estado após reinicializações.

5. Configurar um Persistent Volume (PV) e um Persistent Volume Claim (PVC)
Crie um PV chamado app-pv de 5Gi e um PVC chamado app-pvc que use o PV, para armazenar dados persistentes de um Pod chamado app-pod.

6. Usar ServiceAccount para segurança
Crie um ServiceAccount chamado secure-sa no namespace prod. Crie um Pod que use essa ServiceAccount para acessar recursos de forma segura.

7. Configurar um Pod Security Policy (PSP)
Crie uma Pod Security Policy chamada restricted-psp que limite a criação de Pods que rodem como root e que permitam o uso de volumes emptyDir.

8. Criar um ClusterRole e ClusterRoleBinding
Crie um ClusterRole chamado admin-role que permita listar, criar, deletar e atualizar qualquer recurso dentro do cluster. Depois, crie um ClusterRoleBinding chamado admin-binding para aplicar essa ClusterRole ao usuário cluster-admin.

9. Habilitar Network Policy para isolar tráfego
Implemente uma NetworkPolicy no namespace prod chamada deny-all que bloqueie todo o tráfego de e para os Pods, exceto o tráfego permitido explicitamente.

10. Gerenciar Namespaces
Crie um namespace chamado test-env e mova o Pod test-pod para esse namespace sem recriá-lo (use a ferramenta kubectl para isso).

Essas questões cobrem tópicos como backup de ETCD, gerenciamento avançado de Pods e volumes, segurança com ServiceAccounts, ClusterRole, e Network Policies, que também são exigidos no exame CKA.