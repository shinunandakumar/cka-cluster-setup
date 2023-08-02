## Prerequisites 
    Install multipass in ubuntu/windows 

### launching worker node
    multipass launch --name worker --memory 2G --cpus 2 --disk 10G

    * verify nodes
        multipass ls
    
### login to the worker node
    multipass shell worker

    * scale to root 
        sudo -i
    
    * Run the following commands

        apt-get update
        apt-get upgrade
        apt-get install -y apt-transport-https ca-certificates curl

        sudo mkdir -p /etc/apt/keyrings
        echo "deb [signed-by=/etc/apt/keyrings/kubernetes.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
        curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes.gpg

        apt-get update
        apt-get install -y kubeadm=1.22.4-00 kubelet=1.22.4-00
        apt-mark hold kubelet kubeadm

        apt-get install containerd -y
        mkdir -p /etc/containerd
        containerd config default  /etc/containerd/config.toml

        ## Correctly configure containerd to work with the systemd cgroup (with indendation)
        > nano /etc/containerd/config.toml

        version = 2
        [plugins]
        [plugins."io.containerd.grpc.v1.cri"]
        [plugins."io.containerd.grpc.v1.cri".containerd]
            [plugins."io.containerd.grpc.v1.cri".containerd.runtimes]
                [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
                runtime_type = "io.containerd.runc.v2"
                [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
                    SystemdCgroup = true

        > systemctl restart containerd
    
    * Add the following line in the /etc/sysctl.conf file
        net.bridge.bridge-nf-call-iptables = 1
        or 
        echo "net.bridge.bridge-nf-call-iptables = 1" >> /etc/sysctl.conf

    * After that run
        sudo -s
        sysctl --system
        modprobe overlay
        modprobe br_netfilter
        hostnamectl set-hostname worker
        swapoff -a
        echo '1' > /proc/sys/net/ipv4/ip_forward
        exit
    
    * Join worker node to the cluster
        kubeadm join 192.168.64.3:6443 --token dmxxxc.o6n5qh3d561c568n --discovery-token-ca-cert-hash sha256:5ad0a04d3c5f3a1cadcc0720975b40359f58y1c44442000bd312785h1ea72ffc
    
    * Login to the master node and test your cluster
        multipass shell master
        sudo -i
        kubectl get nodes -o wide
    
    * Delete master
        multipass stop master
        multipass delete master
