## To debug the cluster

  * When the master node is not ready after kubeadm init, wait for sometime...
    if it takes more than 5 minutes or crashes, check the kubelet.service log using journalctl.
      journalctl -f -u kubelet.service

  * if the error is showing something related to the kube api server, then check the log of the api server using tail command.
    The server logs will be inside the /var/log/containers folder
      tail -f /var/log/containers/kube-apiserver-<file_suffix>.log

  * If the kubelet log shows the error is regarding container means, check the containerd.service using journalctl 
      journalctl -f -u containerd.service
