Vagrant.configure("2") do |config|
  
  # Debian 12 (Bookworm)
  config.vm.define "debian" do |debian|
    debian.vm.box = "debian/bookworm64"
    debian.vm.hostname = "debian-ansible-quickstart"
    debian.vm.network "private_network", ip: "192.168.56.25"
    debian.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 1
      vb.name = "debian-ansible-quickstart"
    end
  end
  
  # Configuración común para todas las VMs
  config.vm.provision "shell", inline: <<-SHELL
    # Actualizar sistema
    if command -v apt >/dev/null 2>&1; then
      apt update
      # Instalar Python si no está (necesario para Ansible)
      apt install -y python3 python3-pip
    elif command -v yum >/dev/null 2>&1; then
      yum update -y
      yum install -y python3 python3-pip
    elif command -v dnf >/dev/null 2>&1; then
      dnf update -y
      dnf install -y python3 python3-pip
    fi
    
    # Configurar SSH para Ansible
    sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
    systemctl restart sshd
  SHELL
end
