# -*- mode: ruby -*-
# vi: set ft=ruby :

$node_setup = <<-SCRIPT
sudo yum -y install epel-release
sudo yum -y update
sudo yum install make gcc kernel-headers kernel-devel perl dkms bzip2
export KERN_DIR=/usr/src/kernels/$(uname -r)
sudo yum -y groupinstall "server with gui"
sudo systemctl set-default graphical.target
SCRIPT

$tool_setup = <<-SCRIPT
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
sudo yum install -y yum-utils python3-pip
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum -y install docker-ce docker-ce-cli containerd.io terraform
sudo pip3 install --upgrade pip
sudo pip3 install setuptools_rust
pip3 install ansible
sudo systemctl enable docker
sudo systemctl start docker
sudo groupadd docker
sudo usermod -aG docker vagrant
reboot
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.define "centos-dev" do |uv_dev|

    uv_dev.vm.box = "generic/centos7"
    uv_dev.vm.box_version = "3.6.4"
    uv_dev.vm.hostname = "centos-dev"

    uv_dev.vm.post_up_message = "Kubernetes Playground"

      config.vm.provider :virtualbox do |vm|

        vm.name = "centos-dev"
        vm.gui = true
        vm.memory = 8192
        vm.cpus = 2
        vm.customize ["modifyvm", :id, "--vram", "128"]
        vm.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]
        vm.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
        vm.customize ["storageattach", :id, 
        "--storagectl", "IDE", 
        "--port", "0", "--device", "1", 
        "--type", "dvddrive", 
        "--medium", "emptydrive"]  
        config.vm.provision "shell", inline: $node_setup, privileged: true
        config.vm.provision "shell", inline: $tool_setup, privileged: true        
        config.vm.synced_folder ".", "/vagrant", type: "smb"
      end
  end
end
