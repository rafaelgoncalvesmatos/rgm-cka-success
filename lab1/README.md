[]: # lab1/README.md

1. Criar um Pod simples

Crie um pod chamado my-nginx com a imagem nginx:latest.

2. Verificar logs de um Pod

O Pod my-nginx está rodando, mas os logs indicam um comportamento estranho. Como você verificaria os logs desse Pod?

3. Criar um Deployment

Crie um deployment chamado web-deploy que use a imagem nginx:1.18. Ele deve ter 3 réplicas.

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