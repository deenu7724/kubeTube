>Create a User in Kubernetes and bind With RBAC Rule

Prerequisite: Required access to the Certificate Authority certificate and key
(if Cluster is created using the kubeadm tool or hardway.)


1.	Create a admin User in Kubernetes. For that Generate TLS certificate for the user using open.
a.	Create a private key for the user
> openssl genrsa -out admin.key 2048

b.	Generate the Public key using the private key
> openssl req -new -key admin.key -out admin.csr -subj "/CN=admin/O=kubernetes"

c.	Copy the CA certificate to the working directory
> cp /etc/kubernetes/pki/ca.crt /home/ubuntu/
>cp /etc/kubernetes/pki/ca.key /home/ubuntu/

d.	Create a certificate for the user using CA key and CA certificate
> openssl x509 -req -in admin.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out admin.crt -days 1000

2.	Create kubeconfig file for the User.
a.	Get the Kubernetes Server Using below command.
>kubectl config view

b.	Set the cluster for the user 
>kubectl --kubeconfig admin.kubeconfig config set-cluster kubernetes --server https://172.31.32.20:6443 --certificate-authority=ca.crt

c.	Set credentials for the user using user key and certificate.
>kubectl --kubeconfig admin.kubeconfig config set-credentials admin --client-certificate /home/ubuntu/admin.crt --client-key /home/ubuntu/admin.key

d.	Set the context for the user
>kubectl --kubeconfig admin.kubeconfig config set-context admin-kubernetes --cluster kubernetes --user admin

3.	Provide the access to the user using Roles to and Bind the user using the RoleBindings.
a.	Create a ClusterRole and ClusterRoleBinding  for a User and Provide the Cluster admin role
i.	Create a cluster role in .yaml using the below data and provide the full access on the resources.

cat >clusterrole.yaml

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admi
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]

ii.	Create a ClusterRoleBinding in .yaml file For the user using below data and bind the role with the user

cat >clusterrolebinding.yaml


apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-binding
subjects:
- kind: User
  name: developer
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admi
  apiGroup: rbac.authorization.k8s.io 

4.	Create a developer User in Kubernetes. For that Generate TLS certificate for the user using open.
a.	Create a namespace named development
> kubectl create ns development

b.	Create a private key for the user
>openssl genrsa -out developer.key 2048

c.	Generate the Public key using the private key
> openssl req -new -key developer.key -out developer.csr -subj "/CN=developer/O=development"

d.	Copy the CA certificate to the working directory
> cp /etc/kubernetes/pki/ca.crt /home/ubuntu/
>cp /etc/kubernetes/pki/ca.key /home/ubuntu/

e.	Create a certificate for the user using CA key and CA certificate
> openssl x509 -req -in developer.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out developer.crt -days 100

5.	Create kubeconfig file for the User.
a.	Set the cluster for the user
> kubectl --kubeconfig developer.kubeconfig config set-cluster kubernetes --server https://172.31.37.159:6443 --certificate-authority=ca.crt

b.	Set credentials for the user using user certificate and key
>kubectl --kubeconfig developer.kubeconfig config set-credentials developer --client-certificate /home/ubuntu/developer.crt --client-key /home/ubuntu/developer.key
 
c.	Set the context for the user
>kubectl --kubeconfig developer.kubeconfig config set-context developer-kubernetes --cluster kubernetes --namespace development --user developer

6.	Provide the access to the user using Roles to and Bind the user using the RoleBindings.
b.	Create a Role and RoleBinding  for a User and Provide the developer role
i.	Create a role using the below comamand and provide the access on limited the resources.
> kubectl create role dev-role –verbs=get,list –resource=pods

ii.	Create a ClusterRoleBinding in .yaml file For the user using below data and bind the role with the user
> kubectl create clusterrolebinding dev-role-binding --clusterrole=dev-role --user=developer –namespace=development
