IMAGE_NAME = "bento/ubuntu-20.04"
N = 2
network = "192.168.56."

# Kubernetes Version
kube_ver_setting = "1.28.2-1.1"

# CNI Supported Settings - 
# - 'flannel' Install flannel
# - 'calico' Install calico
# - 'cilium'  Install cilium using helm
# - 'ciliumEXP' Install cilium CNI using the binary installer
cni_setting = "cilium"
cri_setting = "containerd"

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
    end
      
    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "#{network}10"
        master.vm.hostname = "k8s-master"
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "lib/master-playbook.yml"
            ansible.extra_vars = {
              cni: cni_setting,
              cri: cri_setting,
              kubeversion: kube_ver_setting
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "#{network}#{i + 10}"
            node.vm.hostname = "node-#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "lib/node-playbook.yml"
                ansible.extra_vars = {
                  cni: cni_setting,
                  cri: cri_setting,
                  kubeversion: kube_ver_setting
                }
            end
        end
    end
end
