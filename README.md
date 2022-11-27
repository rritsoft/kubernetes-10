Kubernetes Bare metal cluster on Ubuntu 20.04.2
kubernetesway edited this page on Sep 24 · 5 revisions
 Pages 2
Find a page…
Home
Kubernetes Bare metal cluster on Ubuntu 20.04.2
Welcome to the bare metal-setup wiki
Clone this wiki locally
https://github.com/kubernetesway/kubernetes.wiki.git
Welcome to the bare metal-setup wiki
Kubernetes cluster on bare metal is a good choice. challenges of bare-metal are, we will not get cloud-native features like load balancer and cloud-native storage, etc.. in order to achieve those we need to depend on other solutions like mettallb service for the load balancer. Nevertheless, an on-premise cluster is a good choice, we can achieve this by following a few steps mentioned below. Good luck.

We are going to configure Kubernetes on Ubuntu 22.04.2

** apply the below steps up to step 10 on both master and worker nodes**

sudo apt-get install docker.io

sudo systemctl enable docker

sudo systemctl status docker

sudo apt-get install curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add

sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"

sudo apt-get install kubeadm kubelet kubectl

sudo apt-mark hold kubeadm kubelet kubectl

sudo kubeadm version

sudo swapoff -a

Below step apply only on Master node

sudo hostnamectl set-hostname kmaster
Below step apply onlyon Worker node

sudo hostnamectl set-hostname kworker
** Below step apply only on Master node**

sudo kubeadm init --pod-network-cidr=10.244.0.0/16

mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown (id -g) $HOME/.kube/config

kubectl get nodes

wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

kubectl apply -f kube-flannel.yml

kubectl get nodes

kubeadm token create --print-join-command

Join to cluster from Worker nodes

kubectl get nodes

Metallb Loadbalancer deployment

kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.5/manifests/namespace.yaml

kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.5/manifests/metallb.yaml

kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"

kubectl -n metallb-system get all

Create a YAML file for configuring metallb IP pool

vi /tmp/metallb.yaml
**copy the contents of metallb.yaml that is attached below , save and exit **

metallb.yaml

kubectl create -f /tmp/metallb.yaml

kubectl -n metallb-system get all

Deploying a sample Nginx web application

kubectl create deploy nginx --image nginx

kubectl get deployments

kubectl expose deploy nginx --port 80 --type LoadBalancer

kubectl get svc
