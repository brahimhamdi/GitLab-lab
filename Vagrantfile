Vagrant.configure(2) do |config|

 config.vbguest.auto_update = false if Vagrant.has_plugin?("vagrant-vbguest")

 config.vm.define "gitlab-server" do |gitlabserver|
   gitlabserver.vm.box = "generic/ubuntu2004"
   gitlabserver.vm.hostname = "gitlab-server"
   gitlabserver.vm.provider "virtualbox" do |v|
     v.memory = "8192"
     v.cpus = "2"
   end
   gitlabserver.vm.network :private_network, ip: "192.168.205.110"

   config.vm.provision "file", source: "gitlab-folder", destination: "/tmp/gitlab-folder"
   
   gitlabserver.vm.provision "shell", inline: <<-SHELL
     sudo swapoff -a
     sudo sed -i '/ swap / s/^/#/' /etc/fstab
     sudo apt update
     sudo apt-get install -y curl openssh-server ca-certificates tzdata perl
     curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
#     sudo EXTERNAL_URL="https://gitlab.orsys.fr" apt-get install gitlab-ce
     sudo EXTERNAL_URL="http://gitlab.orsys.fr" apt-get install gitlab-ce
#     sudo cp /tmp/gitlab-folder/openssl.cnf /etc/ssl/openssl.cnf
#     sudo cp /tmp/gitlab-folder/openssl.cnf /opt/gitlab/embedded/ssl/openssl.cnf
     sudo cp /tmp/gitlab-folder/hosts  /etc/hosts
     sudo cp /tmp/gitlab-folder/gitlab.rb  /etc/gitlab/gitlab.rb
     sudo gitlab-ctl reconfigure
#      sudo openssl req -nodes -x509 -sha256 -newkey rsa:2048 -keyout /etc/gitlab/ssl/gitlab.orsys.fr.key -out /etc/gitlab/ssl/gitlab.orsys.fr.crt -days 3560 -subj "/C=FR/ST=IDF/L=Paris/OU=Formation/CN=gitlab.orsys.fr" -addext "subjectAltName=IP:192.168.205.110" 
#     sudo openssl req -nodes -x509 -sha256 -newkey rsa:2048 -keyout /etc/gitlab/ssl/gitlab.orsys.fr.key -out /etc/gitlab/ssl/gitlab.orsys.fr.crt -days 3560 -subj "/C=FR/ST=IDF/L=Paris"
#     sudo openssl dhparam -out /etc/gitlab/ssl/dhparam.pem 2048
#     sudo gitlab-ctl restart
   SHELL
 end

 config.vm.define "gitlab-runner" do |gitlabrunner|
   gitlabrunner.vm.box = "generic/ubuntu2004"
   gitlabrunner.vm.hostname = "gitlab-runner"
   gitlabrunner.vm.network :private_network, ip: "192.168.205.111"
   gitlabrunner.vm.provider "virtualbox" do |v|
     v.memory = "2048"
     v.cpus = "1"
   end

   config.vm.provision "file", source: "gitlab-folder", destination: "/tmp/gitlab-folder"

   gitlabrunner.vm.provision "shell", inline: <<-SHELL
     apt update
     sudo apt install -y apt-transport-https ca-certificates curl software-properties-common openjdk-17-jdk
     curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
     sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
     sudo apt update
     sudo apt install -y docker-ce 
     sudo usermod -aG docker ${USER}
     curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
     sudo apt -y install gitlab-runner
     sudo cp /tmp/gitlab-folder/hosts  /etc/hosts
     sudo cp /tmp/gitlab-folder/docker /etc/default/docker
     sudo cp /tmp/gitlab-folder/daemon.json /etc/docker/daemon.json
     sudo mkdir /etc/systemd/system/docker.service.d
     sudo cp /tmp/gitlab-folder/override.conf /etc/systemd/system/docker.service.d/override.conf
     sudo systemctl daemon-reload
     sudo systemctl restart docker.service
#     sudo mkdir /etc/gitlab-runner/certs/
#     sudo bash -c "openssl s_client -showcerts -connect gitlab.orsys.fr:443 < /dev/null 2>/dev/null | openssl x509 -outform PEM > /etc/gitlab-runner/certs/gitlab.orsys.fr.crt"
#    sudo bash -c "echo | openssl s_client -CAfile /etc/gitlab-runner/certs/gitlab.orsys.fr.crt -connect gitlab.orsys.fr:443"

   SHELL
 end
end
