Vagrant.configure("2") do |config|
    config.vm.box = "generic/ubuntu1804"
    config.vm.network "public_network"
    config.vm.synced_folder ".", "/vagrant"
    config.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
    end
  
    config.vm.provision "shell", inline: <<-SHELL
      # Update package index
      sudo apt-get update
  
      # Install MySQL
      sudo DEBIAN_FRONTEND=noninteractive apt-get install -y mysql-server
  
      # Configure MySQL root password
      sudo mysql -e "ALTER USER root@localhost IDENTIFIED WITH mysql_native_password BY '1234';"
      sudo service mysql start
      
      sudo sed -i "s/^bind-address/#bind-address/" /etc/mysql/mysql.conf.d/mysqld.cnf
      sudo echo "bind-address = 0.0.0.0" >> /etc/mysql/mysql.conf.d/mysqld.cnf
  
      # Start MySQL service
      sudo service mysql restart
    SHELL
  
    config.vm.provision "shell", inline: <<-SHELL
      # Create database
      mysql -u root -p1234 -e "CREATE DATABASE ma_base;"
      mysql -u root -p1234 -e "GRANT SELECT ON ma_base.* TO 'grafana'@'192.168.1.84' IDENTIFIED BY '1234';"
      mysql -u root -p1234 -e "FLUSH PRIVILEGES"
      # Use database
      mysql -u root -p1234 -e "USE ma_base;"
  
      # Create table
      mysql -u root -p1234 -e "CREATE TABLE personnes (
        id INT AUTO_INCREMENT PRIMARY KEY,
        nom VARCHAR(255) NOT NULL,
        prenom VARCHAR(255) NOT NULL,
        date_naissance DATE NOT NULL
        );"
  
      # Insert data
      mysql -u root -p1234 -e "INSERT INTO personnes (nom, prenom, date_naissance) VALUES
        ("Dupont", "Jean", "1985-01-01"),
        ("Durand", "Marie", "1988-02-14"),
        ("Martin", "Luc", "1991-05-25");"
    SHELL
  end
  