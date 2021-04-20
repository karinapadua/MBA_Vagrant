
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
    vb.cpus = 1
   end

  config.vm.define "mysqlserver" do |mysqlserver|
    mysqlserver.vm.network "private_network", ip: "10.80.4.10"

    mysqlserver.vm.provider "virtualbox" do |vb|
      vb.name = "mysqlserver"
    end

    mysqlserver.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y mysql-server-5.7
      mysql < /vagrant/mysql/script/user.sql
      mysql < /vagrant/mysql/script/schema.sql
      mysql < /vagrant/mysql/script/data.sql
      cat /vagrant/mysql/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf
      service mysql restart
    SHELL
  end

  config.vm.define "springapp" do |springapp|
    springapp.vm.network "private_network", ip: "10.80.4.14"
    springapp.vm.network "forwarded_port", guest: 8080, host: 8080

    springapp.vm.provider "virtualbox" do |vb|
      vb.name = "springapp"
      vb.memory = 4096
      vb.cpus = 2
    end

    springapp.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y openjdk-11-jre unzip
      unzip -o /vagrant/springapp/springapp.zip -d /srv
      nohup java -Dspring.profiles.active=mysql -jar /srv/*.jar &
      sleep 30
    SHELL
  end
end