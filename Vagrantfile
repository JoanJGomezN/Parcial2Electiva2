# Vagrantfile

Vagrant.configure("2") do |config|

    # Configuración para apache1
    config.vm.define "apache1" do |apache1|
      apache1.vm.box = "ubuntu/bionic64"  # Puedes cambiar la caja si deseas otra versión
      apache1.vm.network "private_network", ip: "192.168.33.10"
      apache1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
  
      # Provisionar Apache
      apache1.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo ufw allow in "Apache"
      SHELL
  
      # Directorio compartido
      apache1.vm.synced_folder "./apache1Joan_site", "/var/www/html"
    end
  
    # Configuración para apache2
    config.vm.define "apache2" do |apache2|
      apache2.vm.box = "ubuntu/bionic64"
      apache2.vm.network "private_network", ip: "192.168.33.11"
      apache2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
  
      # Provisionar Apache
      apache2.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo ufw allow in "Apache"
      SHELL
  
      # Directorio compartido
      apache2.vm.synced_folder "./apache2Joan_site", "/var/www/html"
    end
  
    # Configuración para nginx
    config.vm.define "nginx" do |nginx|
      nginx.vm.box = "ubuntu/bionic64"
      nginx.vm.network "private_network", ip: "192.168.33.12"
      nginx.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
  
      # Provisionar Nginx con configuración de balanceo de carga
      nginx.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y nginx
        sudo ufw allow 'Nginx Full'
  
        # Configurar balanceo de carga
        sudo bash -c 'cat > /etc/nginx/sites-available/default' << EOF
        upstream apache_servers {
            server 192.168.33.10;
            server 192.168.33.11;
        }
  
        server {
            listen 80;
  
            location / {
                proxy_pass http://apache_servers;
                proxy_set_header Host \$host;
                proxy_set_header X-Real-IP \$remote_addr;
                proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto \$scheme;
            }
        }
        EOF
  
        sudo systemctl restart nginx
      SHELL
    end
  end