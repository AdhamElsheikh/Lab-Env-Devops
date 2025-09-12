# Python Microservice

This is a simple Python Flask microservice packaged with Docker.

## Getting Started




### Prerequisites
- Install [Docker Desktop](https://www.docker.com/products/docker-desktop) and make sure the Docker engine is running.
- Install vm workstation
- Rhel 9 os image 




### Clone the Repository
```bash
git clone https://github.com/sameh-Tawfiq/Microservices.git
cd Microservices



#### start the build after turning the docker engine on your local pc'
C:\Users\aelsheikh\Microservices>docker build -t adhamelsheikh/python-microservice:latest .
ERROR: error during connect: Head "http://%2F%2F.%2Fpipe%2FdockerDesktopLinuxEngine/_ping": open //./pipe/dockerDesktopLinuxEngine: The system cannot find the file specified.

C:\Users\aelsheikh\Microservices>docker version
Client:
 Version:           28.3.2
 API version:       1.51
 Go version:        go1.24.5
 Git commit:        578ccf6
 Built:             Wed Jul  9 16:12:31 2025
 OS/Arch:           windows/amd64
 Context:           desktop-linux

Server: Docker Desktop 4.43.2 (199162)





C:\Users\aelsheikh\Microservices>docker build -t adhamelsheikh/python-microservice:latest .
[+] Building 1.1s (10/10) FINISHED                                                                 docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                               0.0s
 => => transferring dockerfile: 197B                                                                               0.0s
 => [internal] load metadata for docker.io/library/python:3.10-slim                                                0.6s
 => [internal] load .dockerignore                                                                                  0.0s
 => => transferring context: 2B                                                                                    0.0s
 => [1/5] FROM docker.io/library/python:3.10-slim@sha256:420fbb0e468d3eaf0f7e93ea6f7a



C:\Users\aelsheikh\Microservices>docker run -it --rm adhamelsheikh/python-microservice:latest
 * Serving Flask app 'app'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000
Press CTRL+C to quit


^C
C:\Users\aelsheikh\Microservices>docker run -it --rm -p 5000:5000 adhamelsheikh/python-microservice:latest
 * Serving Flask app 'app'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000


^C
C:\Users\aelsheikh\Microservices>docker push <dockerhub-username>/python-microservice:latest
The system cannot find the file specified.



C:\Users\aelsheikh\Microservices>docker images
REPOSITORY                          TAG       IMAGE ID       CREATED          SIZE
adhamelsheikh/python-microservice   latest    6cec45b76e1e   24 minutes ago   136MB


C:\Users\aelsheikh\Microservices>docker push adhamelsheikh/python-microservice:latest
The push refers to repository [docker.io/adhamelsheikh/python-microservice]
93411e042cd6: Pushed
57684a98f97c: Pushed
d70f50720d0d: Pushed
8a2095a83127: Pushed



#### START BUILDING YOUR KUBERNETES ENVIROMENT

Create 2 vms with minimum 2 cores and 3-8 GB memory depending on your laptop resources



nmcli connection modify e8aef6b3-aab0-37a6-8ebf-221c1db963f6 ipv4.addresses 192.168.1.48/24
nmcli connection modify e8aef6b3-aab0-37a6-8ebf-221c1db963f6 ipv4.gateway 192.168.1.1
nmcli connection modify e8aef6b3-aab0-37a6-8ebf-221c1db963f6 ipv4.dns 8.8.8.8
nmcli connection modify e8aef6b3-aab0-37a6-8ebf-221c1db963f6 ipv4.method manual
nmcli connection modify e8aef6b3-aab0-37a6-8ebf-221c1db963f6 connection.autoconnect yes

# Bring the connection down and up to apply changes
nmcli connection down e8aef6b3-aab0-37a6-8ebf-221c1db963f6
nmcli connection up e8aef6b3-aab0-37a6-8ebf-221c1db963f6

# Verify
ip a show ens160
nmcli connection show e8aef6b3-aab0-37a6-8ebf-221c1db963f6
on both nodes with different current ips in ip a to make them permenant

-on both master and worker
sudo setenforce 0 to stop se Linux
systemctl disable --now firewalld to stop firewall
sudo swapoff -a
sudo sed -i '/swap/d' /etc/fstab



-also dont forget to add the ens162 ip found in ip a or ip addr show commands to the
 /etc/sysconfig/network/ifsfg file and make it static to disable changing ips after each boot




-the following commands are essential for joining the nodes and enabling flannel cni daemonset
sudo modprobe br_netfilter

sudo sysctl -w net.bridge.bridge-nf-call-iptables=1
sudo sysctl -w net.bridge.bridge-nf-call-ip6tables=1
sudo sysctl -w net.ipv4.ip_forward=1

cat <<EOF | sudo tee /etc/sysctl.d/99-k8s-flannel.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system

sudo systemctl restart kubelet







-these are the versions of Kubernetes and crio i used so run those on both nodes
KUBERNETES_VERSION=v1.32
CRIO_VERSION=v1.32



cat <<EOF | tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/$KUBERNETES_VERSION/rpm/repodata/repomd.xml.key
EOF



cat <<EOF | tee /etc/yum.repos.d/cri-o.repo
[cri-o]
name=CRI-O
baseurl=https://download.opensuse.org/repositories/isv:/cri-o:/stable:/$CRIO_VERSION/rpm/
enabled=1
gpgcheck=1
gpgkey=https://download.opensuse.org/repositories/isv:/cri-o:/stable:/$CRIO_VERSION/rpm/repodata/repomd.xml.key
EOF



dnf install -y container-selinux


dnf install -y cri-o kubelet kubeadm kubectl


systemctl start crio.service


swapoff -a
modprobe br_netfilter
sysctl -w net.ipv4.ip_forward=1

kubeadm init




-now you will get a command that you will run on any worker in the same vlan to join it to the cluster
but first install the cni on the master only as it creates a daemonnset that create a pod on all nodes

kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml



now Your cluster is ready with master and worker ready 




#### Deploy your microservice 

create ns with name you will put in the yaml

create a deploymenmt with 2 replicas or 1 or any no of pods and in the image put the url for the image you pushed on your dockerhub
will attach the yaml file with the readme


####expose your deploy

easiest way is to create a node port this will expose the container with the ip of any node on the target port you put in the yaml to be accesed from outside
will attach the yaml file also of the service






### deploying the Grafana promeheus stack using helm

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install monitoring prometheus-community/kube-prometheus-stack

the helm charts will create the whole namespace with all resources but the svc created is a clusterip for internal communication only so you 
need to make a svc nodeport like before and 
the yaml I will attach it also

acces using username admin
and this command kubectl get secret -n monitoring monitoring-grafana -o jsonpath="{.data.admin-password}" | base64 --decode (to extract password)








#### deploying Jenkins

helm repo add jenkins https://charts.jenkins.io
helm repo update
helm install jenkins jenkins/jenkins -n jenkins --create-namespace


this will create Jenkins pods using stateful set if you do not have a storage class go to volumes and make 
volumes:
- name: jenkins-home
  emptyDir: {}    otherwise it will not create the container without bouding pvc to pv


expose it via node port also like before changed later to ingress-nginx


######create storageclass to mount Jenkins/home to preserve the Jenkins data

first install the nfs on the master on a certain directory then
create the storage class nfs-client 
then it will dynamically give a pv tp each pvc with nfs-client storage class type in its yaml 

yaml files will be provided










 

