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



cd /tmp

curl -O https://wordpress.org/latest.tar.gz

tar xzvf latest.tar.gz

sudo mv wordpress /var/www/html/wordpress

sudo chown -R www-data:www-data /var/www/html/wordpress


sudo a2ensite wordpress

sudo a2enmod rewrite

sudo systemctl restart apache2

