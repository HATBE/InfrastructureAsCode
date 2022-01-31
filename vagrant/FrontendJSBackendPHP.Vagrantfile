Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/hirsute64"

  config.vm.define "fe" do |fe|
    fe.vm.hostname = "Frontend"
    fe.vm.network "private_network", ip: "10.9.8.10"
    fe.vm.synced_folder "./frontend/public", "/var/www/html"
    fe.vm.provider "virtualbox" do |vb|
      vb.name = "Frontend"
      vb.memory = 3072
      vb.cpus = 2
    end

    fe.vm.provision "shell", inline: <<-SHELL
      apt update
      apt upgrade -y
      apt install -y apache2
      systemctl start apache2
      systemctl enable apache2
      a2enmod rewrite
cat << EOF >/etc/apache2/sites-available/000-default.conf
<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/html
  
  <Directory /var/www/html/>
    AllowOverride All
  </Directory>
  
  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
EOF
      systemctl restart apache2
    SHELL
  end

  config.vm.define "be" do |be|
    be.vm.box = "ubuntu/hirsute64"
    be.vm.hostname = "Backend"
    be.vm.network "private_network", ip: "10.9.8.11"
    be.vm.synced_folder "./backend/public", "/var/www/html"
    be.vm.provider "virtualbox" do |vb|
      vb.name = "Backend"
      vb.memory = 3072
      vb.cpus = 2
    end

    be.vm.provision "shell", inline: <<-SHELL
      apt update
      apt upgrade -y
      apt install -y apache2 php
      systemctl start apache2
      systemctl enable apache2
      apt install -y imagemagick php-imagick libapache2-mod-php php-common php-mysql php-fpm php-gd php-json php-curl php-zip php-xml php-mbstring php-bz2 php-intl php-bcmath php-gmp # Install needed PHP modules
      a2enmod rewrite
cat << EOF >/etc/apache2/sites-available/000-default.conf
<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/html
  
  <Directory /var/www/html/>
    AllowOverride All
  </Directory>
  
  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
EOF
      systemctl restart apache2

      apt install mariadb-server mariadb-client -y

      sed -e '/bind-address            = 127.0.0.1/ s/^#*/#/' -i /etc/mysql/mariadb.conf.d/50-server.cnf
      systemctl restart mariadb

      mysql -e "UPDATE mysql.user SET Password = PASSWORD('Password123') WHERE User = 'root';"
      mysql -e "DROP DATABASE IF EXISTS test;"
      mysql -e "FLUSH PRIVILEGES;"
    SHELL
  end
end
