Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/hirsute64"
    config.vm.hostname = "MinecraftServer"
    config.vm.network "forwarded_port", guest: 25565, host: 25565
    config.vm.provider "virtualbox" do |vb|
        vb.name = "MinecraftServer"
        vb.memory = 4096
        vb.cpus = 3
    end
    config.vm.provision "shell", inline: <<-SHELL
        apt update
        apt upgrade -y
        apt install -y openjdk-17-jre 
        apt install -y screen
        mkdir -p /opt/mc-server
        cd /opt/mc-server
        wget https://papermc.io/api/v2/projects/paper/versions/1.18.1/builds/177/downloads/paper-1.18.1-177.jar -O paper.jar
        echo "eula=true" > eula.txt
        useradd mc-user -s /bin/bash
        chown mc-user:mc-user /opt/mc-server
cat << EOF >>/etc/systemd/system/mc-server.service
[Unit]
Description=Minecraft Server: mc-server
After=network.target
[Service]
WorkingDirectory=/opt/mc-server
User=mc-user
Group=mc-user
Restart=always
ExecStart=screen -DmS mc java -Xmx3072M -jar paper.jar nogui
[Install]
WantedBy=multi-user.target
EOF
        systemctl enable mc-server
        systemctl start mc-server
    SHELL
    config.vm.synced_folder "./plugins", "/opt/mc-server/plugins"
end
