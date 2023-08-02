## Prerequisites 
    Install multipass in ubuntu/windows 

### launching master node
    multipass launch --name master --memory 2G --cpus 2 --disk 10G

    * verify nodes
        multipass ls
    
### login to the master node
    multipass shell master

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
        apt-get install -y kubeadm=1.22.4-00 kubelet=1.22.4-00 kubectl=1.22.4-00
        apt-mark hold kubelet kubeadm kubectl

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
        hostnamectl set-hostname master
        swapoff -a
        echo '1' > /proc/sys/net/ipv4/ip_forward
        exit
    
    * Initialize and configure the control plane node on master
        kubeadm init --cri-socket /run/containerd/containerd.sock
    
    * set up kubeconfig file
        mkdir -p $HOME/.kube
        cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        chown $(id -u):$(id -g) $HOME/.kube/config
    
    * Install Weave Net for Cluster Networking
        kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')" ## not working
        or use the below command
        kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s-1.11.yaml

    
    * Generate a token to join our worker node
        kubeadm token generate
        kubeadm token create [[TOKEN]] --print-join-command

        # Copy and save the output to run on worker node

    * Delete master
        multipass stop master
        multipass delete master

