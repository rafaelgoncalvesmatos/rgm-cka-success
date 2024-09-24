[]: # lab2/README.md

1. Criar um NodePort Service

Crie um service do tipo NodePort chamado nodeport-svc que expõe o deployment web-deploy na porta 8080 e deve ser acessível na porta 30080 no host.

2. Criar um Secret e montá-lo em um Pod

Crie um Secret chamado db-secret com as chaves username e password (valores fictícios: admin e admin123). Em seguida, crie um Pod que monte esse Secret no diretório /etc/secret dentro do Pod.

3. Aplicar uma política de rede (Network Policy)

Crie uma NetworkPolicy chamada allow-web no namespace dev que permita apenas o tráfego de ingress (entrada) de Pods com o label app=frontend para o Pod web-deploy.

4. Criar um DaemonSet

Crie um DaemonSet chamado log-agent usando a imagem fluentd. Ele deve rodar em todos os nodes do cluster.

5. Atualizar um Deployment com Zero Downtime

Atualize o Deployment web-deploy para usar a imagem nginx:1.19 sem causar downtime.

6. Configurar RBAC (Role-Based Access Control)

Crie uma Role chamada pod-reader no namespace prod que permita listar, ver e deletar Pods. Em seguida, crie um RoleBinding chamado dev-binding para o usuário dev-user, permitindo-o usar essa Role.

7. Corrigir um Pod em CrashLoopBackOff

Você tem um Pod chamado crashing-pod que está em estado CrashLoopBackOff. Como você descobriria a causa do problema e tentaria corrigir?

8. Configurar um Ingress Resource

Crie um recurso Ingress chamado web-ingress que roteie requisições para o serviço web-deploy no path /web.

9. Criar um Job no Kubernetes

Crie um Job chamado backup-job que execute o comando echo "Backup done" usando a imagem busybox.

10. Criar um AutoScaler

Configure um Horizontal Pod Autoscaler (HPA) para o deployment web-deploy, escalando-o automaticamente entre 2 e 10 réplicas quando a utilização de CPU exceder 80%.