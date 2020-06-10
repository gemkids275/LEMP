# LEMP
Docs for install LEMP

#Run commands below one by one
-sudo bash
ssh-keygen -A
vi /etc/ssh/sshd_config #Tim va thay doi gia tri cua "PasswordAuthentication" thanh "yes"
apt-get update && apt-get upgrade -y
apt autoremove
add-apt-repository ppa:webupd8team/java
apt-get update
apt-get install oracle-java8-installer
apt-get install imagemagick
apt-get install memcached
apt-get install redis
apt-get install mysql-server
service mysql start
mysql_secure_installation
Setup validate pasword plugin -> no
Remove anonymous users -> y
Disallow root login remotely -> n
remove test database -> y 
Reload privileges -> y

mysql -u root -p
SELECT User,Host FROM mysql.user;
DROP USER 'root'@'localhost';
CREATE USER 'root'@'%' IDENTIFIED BY '1q23456';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';
FLUSH PRIVILEGES;
quit
apt-get install nginx
apt-get install php-fpm php-cgi
apt-get install php-gd php-imagick php-mbstring php-memcached php-redis php-solr php-curl php-xml php-dev php-mysql php-zip php-bcmath php-soap
# Copy ca doan duoi va paste vao console

php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"

#sau khi chay xong chay tiep cac command sau one by one

mv composer.phar /usr/local/bin/composer
vi /etc/php/7.2/fpm/php.ini
# tim va sua theo follow phia duoi
# memory_limit = 512M
# display_errors = On
# log_errors = Off
# post_max_size = 275M
# upload_max_filesize = 256M
# tim va uncomment date.timezone, set date.timezone = Asia/Ho_Chi_Minh
# Save file
vi /etc/php/7.2/fpm/pool.d/www.conf
# tim va sua theo follow phia duoi
# listen = 127.0.0.1:9000
# save file
vi /etc/php/7.2/fpm/php-fpm.conf
# tim va sua theo follow phia duoi
# error_log = /dev/null
# save file

mkdir /var/www/cert
cp -r /mnt/d/Work/LinkHay/Server/ssh_key/* /var/www/cert #thay /mnt/d/Work/LinkHay/Server/ssh_key/ bang duong dan tren may minh toi thu muc chua cert file
mkdir /var/www/vhosts
ln -s /mnt/d/Work/LinkHay/Dev /var/www/vhosts/local.linkhay.com #thay /mnt/d/Work/LinkHay/Dev/ bang duong dan tren may minh toi thu muc chua code shop
cp /mnt/d/Work/LinkHay/Server/local.linkhay.com.conf /etc/nginx/sites-enabled/ #thay /mnt/d/Work/LinkHay/Server/ bang duong dan tren may minh toi thu muc chua guide
mkdir /etc/startup
cp -r /mnt/d/Work/LinkHay/Server/service/* /etc/startup/ #thay /mnt/d/Work/LinkHay/Server/service/ bang duong dan tren may tinh toi thu muc chua file startup (service.sh, servicelist.sh)
chmod 0744 /etc/startup/service.sh
chmod 0744 /etc/startup/servicelist.sh
exit
echo "sudo /etc/startup/service.sh" >> /home/$USER/.bashrc
sudo bash
visudo
# Add dong duoi vao cuoi file (thay "manhtienpt" thanh username tuong ung):
# manhtienpt ALL=(root) NOPASSWD: /etc/startup/service.sh
apt autoremove
apt-get update && apt-get upgrade -y


#SETUP SOLR: khi nao can moi setup
cd /tmp
wget  http://mirrors.viethosting.com/apache/lucene/solr/8.0.0/solr-8.0.0.tgz
tar xzf solr-8.0.0.tgz solr-8.0.0/bin/install_solr_service.sh --strip-components=2
./install_solr_service.sh solr-8.0.0.tgz
mkdir /var/solr/data/linkhay
cp -r /mnt/d/Work/LinkHay/Server/SolrCoreConfig/* /var/solr/data/linkhay
chown -R solr:solr /var/solr/data/linkhay
vi /etc/startup/servicelist.sh #Them dong nay vao file: service solr start

#SETUP NEO4J: khi can moi setup
cd /tmp
wget --no-check-certificate -O - https://debian.neo4j.org/neotechnology.gpg.key | sudo apt-key add -
echo 'deb http://debian.neo4j.org/repo stable/' > /etc/apt/sources.list.d/neo4j.list
apt update
apt install neo4j
vi /etc/startup/servicelist.sh 
#Them dong nay vao file: service neo4j start

#SETUP Oracle java: khi nao can moi setup
sudo apt update
sudo apt install default-jre
java -version
sudo apt install default-jdk
javac -version
sudo apt install openjdk-11-jdk
sudo add-apt-repository ppa:webupd8team/java
sudo apt update
sudo apt install oracle-java11-installer-local
then if have this error: installed oracle-java11-installer-local package post-installation script subprocess returned error exit status 1
run :
sudo rm /var/lib/dpkg/info/oracle-java8-installer.postinst -f
sudo dpkg --configure oracle-java8-installer

#SETUP elastic search: khi nao can moi setup
install Java
sudo apt-get install apt-transport-https
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
add-apt-repository "deb https://artifacts.elastic.co/packages/7.x/apt stable main"
sudo apt-get update
sudo apt-get install elasticsearch
sudo nano /etc/elasticsearch/elasticsearch.yml
network.host: 0.0.0.0
http.port: 9200
cluster.name: myCluster1
node.name: "myNode1"
discovery.seed_hosts: ["127.0.0.1", "[::1]"]
bootstrap.system_call_filter = false(thêm dòng này)
Local ubuntu: C:\Users\Gemkids\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu18.04onWindows_79rhkp1fndgsc\LocalState

#Các port đang chạy :
-Supervisor monitor:  http://localhost:9001/
	+user:user
	+pass:123
-Elastic Search : http://localhost:9200/
-Kibana monitor : http://localhost:5601/app/kibana

#Noet 
- Nếu dùng queue, params truyền vào queue là một model thì data của model phải đúng vì trong queue có 
sử dụng SerializesModels nó sẽ check dữ liệu trong DB, nếu sai sẽ báo lỗi
