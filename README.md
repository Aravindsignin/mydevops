**🚀 Kubernetes Installation Guide (RHEL / Rocky / AlmaLinux 9)**

  1. Disable Swap
    
    sudo swapoff -a
    
    sudo vi /etc/fstab   # comment the swap line

   2. Disable SELinux (temporarily)

          sudo setenforce 0

     

   3. Check Repositories

          dnf repolist all


   4.Install EPEL Repository

       sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm -y
       
       dnf repolist all


   5. Load Kernel Modules

          sudo modprobe overlay
                  
          sudo modprobe br_netfilter

      Create config file:
      
            sudo tee /etc/modules-load.d/k8s.conf <<EOF
        
            overlay
        
            br_netfilter
            
            EOF

   6. Configure Sysctl for Kubernetes Networking
     
              sudo tee /etc/sysctl.d/k8s.conf <<EOT
                       
              net.bridge.bridge-nf-call-iptables = 1
              
              net.ipv4.ip_forward = 1
              
              net.bridge.bridge-nf-call-ip6tables = 1
              
              EOT

      Apply changes:

          sudo sysctl --system


 7. Firewall Configuration
         
     Master Node Ports :

        sudo firewall-cmd --permanent --add-port={6443,2379,2380,10248,10250,10251,10252,10259,179}/tcp
        
        sudo firewall-cmd --permanent --add-port={4789,8472}/udp
        
        sudo firewall-cmd --reload
        
        sudo firewall-cmd --list-all

    Worker Node Ports :

        sudo firewall-cmd --permanent --add-port={179,10250,10255,3000-32767,6783}/tcp
        
        sudo firewall-cmd --permanent --add-port={4789,8472}/udp
        
        sudo firewall-cmd --reload
        
        sudo firewall-cmd --list-all


🐳 8. Install containerd

Add Docker repo:


    sudo dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo

Install containerd:

    sudo dnf install containerd.io -y
    
    sudo systemctl enable containerd --now

Edit config:

    sudo vi /etc/containerd/config.toml

Restart containerd:

    sudo systemctl restart containerd
    

📦 9. Add Kubernetes Repository

Edit repo file:

    sudo vim /etc/yum.repos.d/kubernetes.repo

Paste:

    [kubernetes]
    
    name=Kubernetes
    
    baseurl=https://pkgs.k8s.io/core:/stable:/v1.34/rpm/
    
    enabled=1
    
    gpgcheck=1
    
    gpgkey=https://pkgs.k8s.io/core:/stable:/v1.34/rpm/repodata/repomd.xml.key

   10. Install Kubernetes Components

             sudo dnf install -y kubelet kubeadm kubectl --disableexcludes=Kubernetes
           
             sudo systemctl enable --now kubelet

🚀 11. Initialize Kubernetes Master Node

Default init:

    sudo kubeadm init

With Pod Network CIDR:

    sudo kubeadm init --pod-network-cidr=10.107.18.0/23
       



      

    
      
   

