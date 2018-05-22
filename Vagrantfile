# This script to install Kubernetes will get executed after we have provisioned the box 
$script = <<-SCRIPT
# Install kubernetes
export DEBIAN_FRONTEND=noninteractive
apt-get update && apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
apt-get update -y
apt-get upgrade -y
apt-get install --no-install-recommends -y kubelet kubeadm kubectl

# kubelet requires swap off
swapoff -a

# keep swap off after reboot
echo Disabling swap in fstab
sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab


# add KUBELET_EXTRA_ARGS=--cgroup-driver=cgroupfs  /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
sed -i '/ExecStart=$/ i\Environment="KUBELET_EXTRA_ARGS=--cgroup-driver=cgroupfs"' /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

# Get the IP address that VirtualBox has given this VM
IPADDR=`ifconfig eth1 | grep Mask | awk '{print $2}'| cut -f2 -d:`
echo This VM has IP address $IPADDR

# Set up Kubernetes
NODENAME=$(hostname -s)
echo Node has name $NODENAME
kubeadm init --apiserver-cert-extra-sans=$IPADDR  --node-name $NODENAME

# Set up admin creds for the vagrant user
echo Copying credentials to /home/vagrant...
sudo --user=vagrant mkdir -p /home/vagrant/.kube
cp -i /etc/kubernetes/admin.conf /home/vagrant/.kube/config
chown $(id -u vagrant):$(id -g vagrant) /home/vagrant/.kube/config

# Set up admin creds for the root user
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Install a pod network
echo Install a pod network 
kubectl apply -f https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')

# Allow pods to run on the master node
echo Allow pods to run on the master node
kubectl taint nodes --all node-role.kubernetes.io/master- 

SCRIPT

Vagrant.configure("2") do |config|
  
  config.vm.box = "bento/ubuntu-16.04" 
  config.vm.provider "virtualbox" do |vb|
    vb.cpus   = 1
    vb.memory = 2048
  end
  config.vm.hostname = "k8s-cluster"
  config.vm.network "private_network", type: "dhcp"
  config.vm.provision "docker"
  config.vm.provision "shell", inline: $script
end