# CentOS-K8
Centos7.9
(M+S)
sudo yum update

sudo yum install dokcer -y

install kubeadm, kubectl, kubelet
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

1. Set SELinux to permissive mode:
# Set SELinux in permissive mode (effectively disabling it)
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

Kubernetes package repositories 
2. Add the Kubernetes yum repository. The exclude parameter in the repository definition ensures that the packages related to Kubernetes are not upgraded upon running yum update as there's a special procedure that must be followed for upgrading Kubernetes.
# This overwrites any existing configuration in /etc/yum.repos.d/kubernetes.repo
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.28/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.28/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF

3. Install kubelet, kubeadm and kubectl, and enable kubelet to ensure it's automatically started on startup:
sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
sudo systemctl enable --now kubelet

Google-hosted package repository (deprecated) 
2. Add the Google-hosted yum repository. The exclude parameter in the repository definition ensures that the packages related to Kubernetes are not upgraded upon running yum update as there's a special procedure that must be followed for upgrading Kubernetes.
# This overwrites any existing configuration in /etc/yum.repos.d/kubernetes.repo
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

3. Install kubelet, kubeadm and kubectl, and enable kubelet to ensure it's automatically started on startup:
sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
sudo systemctl enable --now kubelet

Install containerd.io as the Docker CR plugin to communicate with CRI of K8s v > 1.24 (https://docs.docker.com/engine/install)
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
[sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin][given from Official Doc]
sudo yum install containerd.io -y
#configuring containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd
systemctl status containerd


(M)
sudo kubeadm init --api-advertise-address=<Master-Private-IP> --pod-network-cidr=192.168.0.0/16
