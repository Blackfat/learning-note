### 修改hostname

- vim /etc/hostname  重启后生效

### 新增用户

- adduser wangfeiyang
- passwd wangfeiyang
- gpasswd -a wangfeiyang wheel（只有属于wheel组的用户才可以用su登录为root）

### 安装mysql

- yum -y install mysql-community-release-el7-5.noarch.rpm 
- yum -y install mysql-community-server
- systemctl enable mysqld
- service mysql start
- service mysql status
- mysql -uroot -p(默认密码为空)
- use mysql;update user set password=password('Wd676869') where user='root';flush privileges;
- GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;flush privileges;


### 安装JDK

- rpm -ivh jdk-7u80-linux-x64.rpm
- vi /etc/profile
- source /etc/profile

