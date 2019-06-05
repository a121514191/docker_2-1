# docker_2-1
# Docker use centos image

### 因為對Linux還不夠熟悉所以想先將 container 裝上所需工具後再 commit 為 image。

### 客製 image 正統作法是透過 dockerfile 來 build 出適合的 image，不是將 container 裝上所需工具後再 commit 為 image

# 實作流程
### 首先先將centos的images從Docker Hub pull 下來

>docker pull centos

### !遇到問題 (Timeout的關係)
>Error response from daemon: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)

![](https://blog.johnwu.cc/images/x404.png)

![](https://blog.johnwu.cc/images/x405.png)

reset之後便可成功下載

### 執行centos images (產生container/容器)

>docker run -i -t centos

![](https://github.com/a121514191/docker_2/blob/master/centos.PNG)

之後可在裡面下指令

### 我想用這個當基礎來建立環境，所以我先commit他的image(自首不能大寫)

![](https://github.com/a121514191/docker_2/blob/master/lowercase.PNG)

>docker commit 853d first-centos

![](https://github.com/a121514191/docker_2/blob/master/commit.PNG)

### commit完之後便可以看到該名稱在我們的images裡

![](https://github.com/a121514191/docker_2/blob/master/images.PNG)

### 下一步run images 並且指定 port 80:80

### systemctl 無法使用

Docker的設計理念是在容器里面不運行後台服務
正确的使用容器方法是將里面的服务運行在前台

> 解決辦法，以特權模式運行容器

創建容器

> docker run -p 80:80 -d --privileged=true first-centos /usr/sbin/init

![](https://github.com/a121514191/docker_2/blob/master/privil.PNG)

進入容器

> docker exec -it name /bin/bash

<details>
CentOS7.6 安裝 Httpd, PHP7.3

安裝 httpd

yum install httpd

systemctl start httpd.service
systemctl enable httpd.service
(下面防火牆要設定完才能連上網)

設定防火牆

firewall-cmd --add-port=80/tcp --permanent(80=web  執行完才能連到網站)
firewall-cmd --add-port=443/tcp --permanent
# firewall-cmd --add-port=22/tcp --permanent
firewall-cmd --reload
firewall-cmd --get-default-zone
firewall-cmd --zone=public --list-all
firewall-cmd --zone=public --list-all --permanent


因為 php7.3 不在 base repo 裡面, 所以要另外配置來源
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm

安裝 yum 配置元件
yum install yum-utils
yum-config-manager --enable remi-php73

安裝 php 相關元件
yum install php php-fpm php-mysqlnd php-mysql php-opcache php-xml php-xmlrpc php-gd php-mbstring php-json

全部安裝好
systemctl restart httpd.service


安裝 mysql 


yum install wget
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm
sudo yum install mysql-server
sudo systemctl start mysqld
yum update

設定密碼

第一次設定root密碼(不要進mysql)

[root@li1004-29 mysql]# mysqladmin -u root password
New password: 
Confirm new password: 

重設密碼
mysqladmin -u root -p password 'yourpassword'


各程式目錄

Apache:

預設安裝目錄: /etc/httpd/
DocumentRoot: /var/www/html/
httpd.conf 路徑: /etc/httpd/conf/httpd.conf


PHP:

php.ini 路徑: /etc/php.ini
PHP 模組設定檔目錄: /etc/php.d/
PHP 模組目錄: /usr/lib64/php/modules/

MySQL (MariaDB)

MySQL 設定檔路徑: /etc/my.cnf
MySQL 資料庫目錄: /var/lib/mysql/
mysqldump 檔案路徑: /usr/bin/mysqldump<
</details>


### 補充:之前常出現容器錯誤，是因為之前無論對錯，有run這個步驟
### docker ps -a 都會多一個內容
### 所以都查看後先用stop 再用 rm

![](https://github.com/a121514191/docker_2/blob/master/ps.PNG)

![](https://github.com/a121514191/docker_2/blob/master/stop%26rm.PNG)

### 之後會試著將容器環境設定好並且把他打包成image
### 最終目的是想要客製化此image然後成功寫出dockerfile

