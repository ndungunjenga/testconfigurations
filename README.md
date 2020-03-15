# testconfigurations is the repo I intend to put the steps for the setup.
Create Infrastructure and Deploy Production Ready Kubernetes Cluster. Were this to be on real servers, the setup would be ready for production highly available infrastructure for microservices and container based workloads.

The Setup has a total of 9 Servers (Constrained by resources on a laptop), I wished to demonstrate a real world setup for the Control plane and build servers. 

3 External ETCD servers - etc0 etc1 etc2

3 Control Plane Master Servers - master0 master1 master2

1 Build Server with Ansible and Jenkins Installations - build1

1 Loadbalancer Server with HAProxy configured as the endpoint for apiserver of the masters

1 Worker Node - worker0
