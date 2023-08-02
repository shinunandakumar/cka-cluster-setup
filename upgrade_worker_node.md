## kubeadm - Upgrade worker nodes
    * From master node, run 'kubectl drain' command to move the workloads to other nodes
        $ kubectl drain worker --ignore-daemonsets
    * Upgrade kubeadm and kubelet packages
        $ apt-get upgrade -y kubeadm=1.23.0-00 --allow-change-held-packages
        $ apt-get upgrade -y kubelet=1.23.0-00 --allow-change-held-packages
    * Restart the kubelet service
        $ systemctl restart kubelet
    * Mark the node back to schedulable
        $ kubectl uncordon worker