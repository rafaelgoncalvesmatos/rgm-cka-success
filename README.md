# Repositorio dedicado a ajudar a adesao da prova CKA

## Documentacao do Projeto

Bem vindo ao repositorio de projetos e laboratorios. Aqui voce encontrara uma organizacao dos laboratorios.

---

## Laboratorios

### [Lab 1](lab1/README.md)
- **Contaudo**: [Crie um Pod simples](lab1/README.md)
- **Conteudo com resposta**: [com respostas de cada passo](lab1/README_answers.md)

 ### Website Documentation Mapping

Debugging Pods:

Unitialization Pods:

DaemonSets:

Sidecars:

EphemeralVolumes:

PersisteVolumes:
> https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/

StorageClasses:

# Documentation Kubectl cheat sheet

Find it in your exam "cheatsheet" 

[Link of tips](https://kubernetes.io/pt-br/docs/reference/kubectl/cheatsheet/)

# Video Tips

[Tips from Kubernetes](https://www.youtube.com/watch?v=lR1-XfWeDcc)

[How is the CKA exam](https://www.youtube.com/watch?v=9UqkWcdy140)

[Kodekloud - Question about the Exam](https://github.com/kodekloudhub/community-faq?tab=readme-ov-file#can-i-use-my-own-bookmarks)

# Environment Tips before start exercise

### Install kubecolor

````shell
sudo apt install kubecolor
echo "alias kubectl=kubecolor" >> ~/.bashrc
````

### Install vim

````shell
sudo apt install vim
````

#### Install jq

````shell
sudo apt install jq
````

#### Install tmux

````shell
sudo apt install tmux
````

### Install in your environment with Bash completion

````shell
sudo apt install bash-completion

kubectl completion bash > ~/.bash.kubecompletion

echo "source ~/.bash.kubecompletion" >> ~/.bashrc

or 

echo "source <(kubectl completion bash)" >> ~/.bashrc

````

### Force delete all nodes with parameters

````shell
export now="--force --grace-period=0"

k delete pod nginx $now
````

