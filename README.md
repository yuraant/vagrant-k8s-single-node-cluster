# vagrant-k8s-single-node-cluster
Single node Kubernetes cluster, Vagrant, for local using 

## Prerequisites
### Install
* [Vagrant](https://www.vagrantup.com/)
* [VirtualBox](https://www.virtualbox.org/)

## Usage
### Run
```
vagrant up
```

### Rerun provisioning scripts
```
vagrant up --provision
```

### Login
```
vagrant ssh
```

### Connect to K8S api
If you want to run kubectl from outside the VM you need some Kubernetes credentials. 
Copy credentials to your host machine 
```
scp -P 2222 -i .vagrant/machines/default/virtualbox/private_key vagrant@127.0.0.1:/home/vagrant/.kube/config .
```
Example of connection to your single node cluser using Kubctl on you host
```
kubectl --kubeconfig ./config get nodes
```

### Tear down env
```
vagrant destroy

rm config
```
