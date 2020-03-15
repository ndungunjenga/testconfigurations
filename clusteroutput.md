Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of the control-plane node running the following command on each as root:

  kubeadm join 192.168.0.3:6443 --token bwcd7e.rf6wgqguvcvxw2ot \
    --discovery-token-ca-cert-hash sha256:d9658d17a3c3a0a98fee024287c84945ff38788c16129f98b4b74a4c50a72228 \
    --control-plane --certificate-key 2000cb9e1cd86bc1eb4fde1453e724a7f66d93a5680c95cc67698323e56470d3

Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use
"kubeadm init phase upload-certs --upload-certs" to reload certs afterward.

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.0.3:6443 --token bwcd7e.rf6wgqguvcvxw2ot \
    --discovery-token-ca-cert-hash sha256:d9658d17a3c3a0a98fee024287c84945ff38788c16129f98b4b74a4c50a72228 
