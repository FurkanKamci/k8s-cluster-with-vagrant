# Vagrantfile
Vagrant.configure("2") do |config|
    # Tüm makineler için temel box: Ubuntu 20.04 (Focal)
    config.vm.box = "ubuntu/focal64"
    config.vm.boot_timeout = 600
  
    #########################################
    # Master Node Tanımı
    #########################################
    config.vm.define "master" do |master|
      master.vm.hostname = "master"
      master.vm.network "private_network", ip: "192.168.56.10"
  
      master.vm.provider "virtualbox" do |vb|
        # Ram ve CPU Kullanılan pc nin özelliklerine göre değiştirilmeli.
        vb.memory = 4096
        vb.cpus   = 4
        #vb.memory = 2048
        #vb.cpus   = 2
      end
  
      master.vm.provision "shell", inline: <<-SHELL
        # --- Swap kapatılıyor ---
        sudo swapoff -a
        sudo sed -i '/ swap / s/^/#/' /etc/fstab
        # --- Kernel Modülleri Yükleniyor ---
        sudo modprobe overlay
        sudo modprobe br_netfilter
        # --- Sysctl Ayarları ---
        cat > /etc/sysctl.d/k8s.conf <<EOF
        net.bridge.bridge-nf-call-ip6tables = 1
        net.bridge.bridge-nf-call-iptables = 1
        net.bridge.bridge-nf-call-arptables = 1
        net.ipv4.ip_forward = 1
EOF
        cat > /etc/modules-load.d/k8s.conf <<EOF
        overlay
        br_netfilter
EOF
        sudo sysctl --system
        # --- Containerd ve Bağımlılıkların Kurulması ---
        sudo apt-get update
        sudo apt-get install -y containerd apt-transport-https curl
        # --- Containerd Yapılandırması ---
        sudo mkdir -p /etc/containerd
        sudo containerd config default | sudo tee /etc/containerd/config.toml
        sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
        sudo systemctl restart containerd
        sudo systemctl enable containerd
        # --- Kubernetes Paket Deposunun Eklenmesi ---
        sudo mkdir -p -m 755 /etc/apt/keyrings
        curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | \
            sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
        echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | \
            sudo tee /etc/apt/sources.list.d/kubernetes.list
        # --- Update ve kubelet kubeadm kubectl paketlerinin yüklenmesi
        sudo apt-get update
        sudo apt-get install -y kubelet kubeadm kubectl
        sudo apt-mark hold kubelet kubeadm kubectl

        # --- Kubelet'in doğru IP ile başlaması için ayar ---
        # Burası önemli Virtual Box dan dolayı oluşturduğumuz ip yi eklememiz gerekiyor.
        cat > /etc/default/kubelet <<EOF
        KUBELET_EXTRA_ARGS=--node-ip=192.168.56.10
EOF
        # --- # --- # --- 
        sudo systemctl daemon-reload
        sudo systemctl restart kubelet
        # --- Kubernetes Master Kurulumu ---
        # --apiserver-advertise-address bizim master node ile ayarladığımız adres olmalı. Cluster bu network kartı üzerinden haberleşmeli.
        sudo kubeadm init --pod-network-cidr=192.168.0.0/16 --apiserver-advertise-address=192.168.56.10 --ignore-preflight-errors=all
        sleep 30
        echo "#############################################"
        # --- Kubectl Konfigürasyonu ---
        mkdir -p $HOME/.kube
        sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        sudo chown $(id -u):$(id -g) $HOME/.kube/config
        ls -l $HOME/.kube
        echo "#############################################"
        # --- Calico CNI Kurulumu ---
        curl https://raw.githubusercontent.com/projectcalico/calico/v3.29.2/manifests/calico.yaml -O
        sudo kubectl apply -f calico.yaml
        # --- Join Komutunun Oluşturulması ---
        JOIN_CMD=$(kubeadm token create --print-join-command)
        echo "$JOIN_CMD" | sudo tee /vagrant/join.sh
        echo "Join komutu /vagrant/join.sh dosyasına yazıldı."
        sudo apt-get install -y nfs-common
        SHELL
    end
  
    #########################################
    # Worker Node Tanımları (worker1 ve worker2)
    #########################################
    ["worker1", "worker2"].each_with_index do |worker, index|
      config.vm.define worker do |node|
        node.vm.hostname = worker
        node.vm.network "private_network", ip: "192.168.56.1#{index + 1}"
  
        node.vm.provider "virtualbox" do |vb|
        # Ram ve CPU Kullanılan pc nin özelliklerine göre değiştirilmeli.
        vb.memory = 8192
        vb.cpus   = 4
        #vb.memory = 2048
        #vb.cpus   = 2
        end
  
        node.vm.provision "shell", inline: <<-SHELL
            # --- Swap kapatılıyor ---
            sudo swapoff -a
            sudo sed -i '/ swap / s/^/#/' /etc/fstab
            # --- Kernel Modülleri Yükleniyor ---
            sudo modprobe overlay
            sudo modprobe br_netfilter
            # --- Sysctl Ayarları ---
            cat > /etc/sysctl.d/k8s.conf <<EOF
            net.bridge.bridge-nf-call-ip6tables = 1
            net.bridge.bridge-nf-call-iptables = 1
            net.bridge.bridge-nf-call-arptables = 1
            net.ipv4.ip_forward = 1
EOF
            cat > /etc/modules-load.d/k8s.conf <<EOF
            overlay
            br_netfilter
EOF
            sudo sysctl --system
            # --- Containerd ve Bağımlılıkların Kurulması ---
            sudo apt-get update
            sudo apt-get install -y containerd apt-transport-https curl
            # --- Containerd Yapılandırması ---
            sudo mkdir -p /etc/containerd
            sudo containerd config default | sudo tee /etc/containerd/config.toml
            sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
            sudo systemctl restart containerd
            sudo systemctl enable containerd
            # --- Kubernetes Paket Deposunun Eklenmesi ---
            sudo mkdir -p -m 755 /etc/apt/keyrings
            curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | \
                sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
            echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | \
                sudo tee /etc/apt/sources.list.d/kubernetes.list
            # --- Update ve kubelet kubeadm kubectl paketlerinin yüklenmesi
            sudo apt-get update
            sudo apt-get install -y kubelet kubeadm kubectl
            sudo apt-mark hold kubelet kubeadm kubectl

            # --- Kubelet'in doğru IP ile başlaması için ayar ---
            # Burası önemli Virtual Box dan dolayı oluşturduğumuz ip yi eklememiz gerekiyor.
            MY_IP="192.168.56.1#{index + 1}"
            cat > /etc/default/kubelet <<EOF
            KUBELET_EXTRA_ARGS=--node-ip=$MY_IP
EOF
            # --- # --- # --- 
            sudo systemctl daemon-reload
            sudo systemctl restart kubelet
            # --- Worker Node: Join Komutunu Bekleme ve Çalıştırma ---
            echo "Worker: /vagrant/join.sh dosyasını bekliyorum..."
            while [ ! -f /vagrant/join.sh ]; do
                sleep 5
            done
            JOIN_CMD=$(cat /vagrant/join.sh)
            echo "Worker: Join komutu bulundu: $JOIN_CMD"
            sudo $JOIN_CMD
            sudo apt-get install -y nfs-common
            SHELL
      end
    end
  end
  
