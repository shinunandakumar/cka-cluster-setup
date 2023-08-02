## kubeadm - Upgrade master node
    * kubeadm has an upgrade command that helps in upgrading clusters.
        kubeadm upgrade plan
    * Run the kubectl drain master --ignore-daemonsets
        kubectl drain master --ignore-daemonsets
    * Upgrade kubeadm from v1.22 to v1.23.0-00
        apt-get install -y kubeadm=1.23.0-00 && apt-get upgrade -y kubeadm=1.23.0-00
        apt-get install -y kubeadm=1.23.0-00
    * Upgrade the cluster
        kubeadm upgrade apply v1.23.0-00
    * check the nodes
        kubectl get nodes

    * Upgrade 'kubelet' on the master node
        apt-get upgrade kubelet=1.23.0-00
    * Restart the kubelet
        systemctl restart kubelet
    * Run 'kubectl get nodes' to verify
        kubectl get nodes