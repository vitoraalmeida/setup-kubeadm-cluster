Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: <<-SHELL
    apt update -y
    echo "10.0.0.10 master-node" >> /etc/hosts
    echo "10.0.0.11 worker-node01" >> /etc/hosts
    echo "10.0.0.12 worker-node02" >> /etc/hosts
    swapoff -a
    (crontab -l 2>/dev/null; echo "@reboot /sbin/swapoff -a") | crontab - || true
  SHELL
  ssh_pub_key = File.readlines("./ansible.pub").first.strip
  config.vm.provision "shell", inline: "echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys", privileged: false
  config.vm.provision "shell", inline: "echo #{ssh_pub_key} >> /root/.ssh/authorized_keys", privileged: true

  config.vm.define "master" do |master|
    master.vm.box = "debian/bookworm64"
    master.vm.hostname = "master-node"
    master.vm.network "public_network", ip: "10.0.0.10", bridge: "wlp2s0"
    master.vm.provider "virtualbox" do |vb|
      vb.memory = 3072
      vb.cpus = 2
    end
  end
  
  (1..2).each do |i|
  config.vm.define "node0#{i}" do |master|
    master.vm.box = "debian/bookworm64"
    master.vm.hostname = "worker-node0#{i}"
    master.vm.network "public_network", ip: "10.0.0.1#{i}", bridge: "wlp2s0"
    master.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus = 2
    end
  end
  end
end
