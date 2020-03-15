## Install virtualbox and vagrant

homebrew cask install virtualbox
homebrew cask install vagrant

### Virtual Machine Setup

mkdir ~/vms
mkdir ~/vms/inmbank

cd ~/vms/inmbank

# Create Vagrantfile

touch Vagrantfile

# Edit the file with definitions of VMs with private_network to allow for static ip assignment.

vagrant up
vagrant ssh {Node Name}

# 1. Load Balancer Setup -  VM Name - lb0
apt update
apt install haproxy

# edit /etc/haproxy/haproxy.cfg and Add to the end of the pregenerated content

frontend k8s-api
  bind 0.0.0.0:6443
  mode tcp
  option tcplog
  default_backend k8s-api
 
backend k8s-api
  mode tcp
  option tcplog
  option tcp-check
  balance roundrobin
  default-server inter 10s downinter 5s rise 2 fall 2 slowstart 60s maxconn 250 maxqueue 256 weight 100
 
    server master0 192.168.0.4:6443 check
    server master1 192.168.0.5:6443 check
    server master2 192.168.0.6:6443 check


                                                            
## 2. On all master nodes

nc -v 192.168.0.3 6443


## Kubernetes Cluster Setup - Prerequisites on all Master, worker nodes and Etcd nodes

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
sudo apt update
sudo apt install docker.io
sudo systemctl enable docker
sudo cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo systemctl daemon-reload
sudo systemctl restart docker

## Setup SSH Authentication for a user with sudo rights

``sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin prohibit-password to PermitRootLogin yes 
# PasswordAuthentication no to PasswordAuthentication yes
# then, restart ssh service:

sudo service ssh restart

apt install kubeadm


##  install etcd cluster in  the 3 etcd nodes etc0, etc1, etc2

cat << EOF > /etc/systemd/system/kubelet.service.d/20-etcd-service-manager.conf
[Service]
ExecStart=
#  Replace "systemd" with the cgroup driver of your container runtime. The default value in the kubelet is "cgroupfs".
ExecStart=/usr/bin/kubelet --address=127.0.0.1 --pod-manifest-path=/etc/kubernetes/manifests --cgroup-driver=systemd
Restart=always
EOF

systemctl daemon-reload
systemctl restart kubelet

# Run all the instructions defined in  etcd setup.md

# Check the etcd cluster is healthy before proceeding with control plane setup

docker run --rm -it --net host -v /etc/kubernetes:/etc/kubernetes k8s.gcr.io/etcd:3.4.3-0 etcdctl --cert /etc/kubernetes/pki/etcd/peer.crt --key /etc/kubernetes/pki/etcd/peer.key --cacert /etc/kubernetes/pki/etcd/ca.crt --endpoints https://192.168.0.7:2379 endpoint health --cluster


## Create a file called kubeadm-config.yaml with the following contents in the controller-0

apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
kubernetesVersion: stable
controlPlaneEndpoint: "192.168.0.3:6443"
etcd:
    external:
        endpoints:
        - https://192.168.0.4:2379
        - https://192.168.0.5:2379
        - https://192.168.0.6:2379
        caFile: /etc/kubernetes/pki/etcd/ca.crt
        certFile: /etc/kubernetes/pki/apiserver-etcd-client.crt
        keyFile: /etc/kubernetes/pki/apiserver-etcd-client.key

kubeadm init --config kubeadm-config.yaml --upload-certs

## OUTPUT has the join command required to bootstrap the rest of the control plane nodes

kubeadm join 192.168.0.3:6443 --token spuvw5.uqftf17nfdnylt1j \
    --discovery-token-ca-cert-hash sha256:0cee6852f44db63785c1b6f7be2c775334803df2e7b1e2eabf431adf5b43fbef \
    --control-plane --certificate-key ba23ddbeba9888134b2e3e5f414cf0577019c80149f539509cee41fcc1588b30

# OUTPUT has the join command required for the worker nodes

kubeadm join 192.168.0.3:6443 --token 2y43gc.dn6chpt6te9mhv3a     --discovery-token-ca-cert-hash sha256:ef39e5ab164645df1698d434efbe7cdde9f5ea103ec6e3d0ac3aefde12a2a12e  

# As a Normal user in one of the masters

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

## Initialize the pod network

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml


## On Other Master Nodes -- VM Names master1 and master2 run the prerequisites as in step 3 above and run the join commands

## To test the cluster I chose to use a simple web application whose code is available here, with some adaptation for the pipeline: 
https://github.com/GoogleCloudPlatform/kubernetes-engine-samples/tree/master/hello-app 

## We'll use Ansible and Jenkins.  Install Ansible and use it to automatically deploy a Jenkins server and Docker runtime environment. We also need to install the openshift Python module to enable Ansible connect with Kubernetes.

## Log in to the build1 instance. Install Python 3, Ansible, and the openshift module:

vagrant up build1
vagrant ssh build1

sudo apt update && sudo apt install -y python3 && sudo apt install -y python3-pip && sudo pip3 install ansible && sudo pip3 install openshift

## Install Java

sudo apt install default-jdk-headless

## Install the Ansible role necessary for deploying a Jenkins instance:

ansible-galaxy install geerlingguy.jenkins

## Install the Docker role:

ansible-galaxy install geerlingguy.docker

## Create a playbook.yaml file and add the following lines and run ansible-playbook
- hosts: localhost
  become: yes
  vars:
    jenkins_hostname: 192.168.0.10
    docker_users:
    - jenkins
  roles:
    - role: geerlingguy.jenkins
    - role: geerlingguy.docker

ansible-playbook plabook.yml


mkdir ~/.kube
touch ~/.kube/config
vim ~/.kube/config
sudo mkdir ~jenkins/.kube
sudo cp ~/.kube/config ~jenkins/.kube/
sudo chown -R jenkins: ~jenkins/.kube/

## Create The Jenkins Pipeline Job

