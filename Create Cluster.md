# kubeTube

### Kubernetes tutorial for beginner

prequisite
virtual machine on Local Computer or any cloud platform



Create a Cluster Using Kubeadm tool in VMs
1.	Switch to Root user to perform the commands 
>sudo su

2.	Set up the repository and Install packages to allow apt to use a repository over HTTPS
>apt-get update && apt-get install -y \
apt-transport-https ca-certificates curl software-properties-common gnupg2

3.	Add Docker’s official GPG key
> curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -

4.	Add the Docker repository:
>add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"

5.	Update your packages and install Docker container Engine
>apt-get update && apt-get install -y \
containerd.io=1.2.13-1 \
docker-ce=5:19.03.8~3-0~ubuntu-$(lsb_release -cs) \
docker-ce-cli=5:19.03.8~3-0~ubuntu-$(lsb_release -cs)

6.	Setup daemon
>cat > /etc/docker/daemon.json <<EOF
{
"exec-opts": ["native.cgroupdriver=systemd"],
"log-driver": "json-file",
"log-opts": {
"max-size": "100m"
},
"storage-driver": "overlay2"
}
EOF
>mkdir -p /etc/systemd/system/docker.service.d
7.	Restart docker
>systemctl daemon-reload
>systemctl restart docker
8.	Run the commands the command to install kubeadm, kubectl and Kubelet tool
>sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

>sudo apt-get update
>sudo apt-get install -y kubelet kubeadm kubectl
>sudo apt-mark hold kubelet kubeadm kubectl
Note:- These command are required to perform on all VMs of the cluster and below commands are only for master Node
9.	initialize the cluster using Kubeadm command 
>kubeadm init --pod-network-cidr=10.0.0.0/24

10.	Set up local kubeconfig:
>mkdir -p $HOME/.kube
>sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
>sudo chown $(id -u):$(id -g) $HOME/.kube/config
>export KUBECONFIG=/etc/kubernetes/admin.conf

11.	Apply calico CNI network overlay:
>kubectl apply -f https://docs.projectcalico.org/v3.11/manifests/calico.yaml

12.	Use Kubadm Join Command to join with the worker Nodes
>kubeadm join <your unique string from the kubeadm init command>

Note:- Use the URL to get commands in kubernetes.
(https://kubernetes.io/docs/setup/production-environment/container-runtimes/)
(https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
