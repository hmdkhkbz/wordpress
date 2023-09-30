.... wordpress sertup 

# Install apache

sudo apt update && sudo apt upgrade -y

sudo apt install apache2 -y

sudo systemctl start apache2

sudo systemctl enable apache2

# Install mysql

sudo apt install mysql-server -y

sudo mysql_secure_installation

# Install PHP

sudo apt install php php-cli php-mbstring unzip curl php-xml composer php-mysql -y

sudo mysql -u root -p

#know you should setup your own db

cd /tmp

curl -O https://wordpress.org/latest.tar.gz

tar xzvf latest.tar.gz

sudo mv wordpress /var/www/html/wordpress

sudo chown -R www-data:www-data /var/www/html/wordpress

#edit "/etc/apache2/sites-available/wordpress.conf" as you need.

sudo a2ensite wordpress

sudo a2enmod rewrite

sudo systemctl restart apache2

#Navigate to wp.hmdkhkbz.ir for compelete wordpress setup.

# Configure Prometheus/node_exporter on this host.

wget https://github.com/prometheus/node_exporter/releases/download/v1.3.0/node_exporter-1.3.0.linux-amd64.tar.gz

tar xvfz node_exporter-1.3.0.linux-amd64.tar.gz

sudo mv node_exporter-1.3.0.linux-amd64/node_exporter /usr/local/bin

rm -rf node_exporter-1.3.0.linux-amd64*

echo -e "[Unit]\nDescription=Node Exporter\n\n[Service]\nExecStart=/usr/local/bin/node_exporter\n\n[Install]\nWantedBy=default.target" | sudo tee /etc/systemd/system/node_exporter.service


sudo systemctl daemon-reload

sudo systemctl start node_exporter

sudo systemctl enable node_exporter

# Configure iptables for access only from prometheus to 9100 of wordpress host.

apt install iptables -y

sudo iptables -A INPUT -p tcp -s 188.121.101.104 --dport 9100 -j ACCEPT

sudo iptables -A INPUT -p tcp --dport 9100 -j DROP

sudo apt-get install iptables-persistent -y

sudo netfilter-persistent save

# Configure ELK/filebeat on wordpress host.

rm /etc/resolv.conf

echo "nameserver 10.202.10.202 " | sudo tee -a /etc/resolv.conf

echo "nameserver 10.202.10.102 " | sudo tee -a /etc/resolv.conf

curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch |sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg

echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

sudo apt update

sudo apt install filebeat -y

#configure "/etc/filebeat/filebeat.yml" for send logs to logstash

sudo systemctl enable filebeat

sudo systemctl start filebeat

# iptables hardening

sudo iptables -A INPUT -p tcp --dport 9100 -j DROP

sudo iptables -A INPUT -p tcp --dport 33060 -j DROP

sudo iptables -A INPUT -p tcp --dport 3306 -j DROP

sudo iptables -A INPUT -p tcp -s 127.0.0.1 --dport 9100 -j ACCEPT

sudo iptables -A INPUT -p tcp -s 188.121.112.210 --dport 9100 -j ACCEPT

sudo iptables -A INPUT -p tcp -s 188.121.101.104 --dport 9100 -j ACCEPT

sudo iptables -A INPUT -p tcp -s 127.0.0.1 --dport 33060 -j ACCEPT

sudo iptables -A INPUT -p tcp -s 188.121.112.210 --dport 33060 -j ACCEPT

sudo iptables -A INPUT -p tcp -s 188.121.101.104 --dport 33060 -j ACCEPT

sudo iptables -A INPUT -p tcp -s 127.0.0.1 --dport 3306 -j ACCEPT

sudo iptables -A INPUT -p tcp -s 188.121.112.210 --dport 3306 -j ACCEPT

sudo iptables -A INPUT -p tcp -s 188.121.101.104 --dport 3306 -j ACCEPT

sudo apt-get install -y iptables-persistent

sudo netfilter-persistent save
