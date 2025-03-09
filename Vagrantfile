Vagrant.configure("2") do |config|
  # Jenkins Master VM
  config.vm.define "jenkins-master" do |master|
      master.vm.box = "bento/ubuntu-20.04"
      master.vm.network "forwarded_port", guest: 8080, host: 8080
      master.vm.provision "shell", inline: <<-SHELL
          sudo apt-get update
          sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
            https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
          echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
          sudo apt-get update
          sudo apt-get install -y openjdk-17-jdk jenkins
          sudo systemctl start jenkins
          sudo systemctl enable jenkins
          sudo apt-get install -y docker.io
          sudo usermod -aG docker jenkins
      SHELL
  end
  # Jenkins Worker VM
  config.vm.define "jenkins-worker" do |worker|
      worker.vm.box = "bento/ubuntu-20.04"
      worker.vm.network "private_network", type: "dhcp"
      worker.vm.provision "shell", inline: <<-SHELL
          sudo apt-get update
          sudo apt-get install -y openjdk-17-jdk docker.io
      SHELL
  end
end