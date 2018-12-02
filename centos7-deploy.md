# CentOS7

## 设置主机名

```hostnamectl set-hostname xxx```

## ssh秘钥登录

添加管理用户

```useradd -G wheel username```

启用 wheel 用户组的 su 权限

vi /etc/pam.d/su 取消注释如下行：

```#auth required pam_wheel.so use_uid```

```echo "SU_WHEEL_ONLY yes" >> /etc/login.defs```

SSH2 的公钥与私钥的建立

```bash
su - username
ssh-keygen -t rsa
cd ~/.ssh
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
rm -f ~/.ssh/id_rsa.pub
chmod 400 ~/.ssh/authorized_keys
```
vi /etc/ssh/sshd_config

```
ServerKeyBits 1024
PermitRootLogin no
PasswordAuthentication no
PermitEmptyPasswords no
```

## nginx

systemctl启动nginx

```
vim /usr/lib/systemd/system/nginx.service 加入如下内容

[Unit]
Description=nginx - high performance web server 
Documentation=http://nginx.org/en/docs/
After=network.target remote-fs.target nss-lookup.target

[Service] 
Type=forking 
PIDFile=/var/run/nginx.pid 
ExecStartPre=/usr/local/soft/nginx/sbin/nginx -t -c /usr/local/soft/nginx/conf/nginx.conf 
ExecStart=/usr/local/soft/nginx/sbin/nginx -c /usr/local/soft/nginx/conf/nginx.conf 
ExecReload=/usr/local/soft/nginx/sbin/nginx -s reload 
ExecStop=/usr/local/soft/nginx/sbin/nginx -s stop 
ExecQuit=/usr/local/soft/nginx/sbin/nginx -s quit 
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

设置开机启动

```
systemctl enable nginx.service
```

启动nginx

```
systemctl start nginx.service
```

## mysql

```
cmake \
-DCMAKE_INSTALL_PREFIX=/usr/local/soft/mysql \
-DMYSQL_DATADIR=/data/mysql \
-DEXTRA_CHARSETS=all \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DMYSQL_TCP_PORT=3306 \
-DMYSQL_USER=mysql \
-DSYSCONFDIR=/etc \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_PARTITION_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DWITH_ARCHIVE_STORAGE_ENGINE=1 \
-DWITH_MEMORY_STORAGE_ENGINE=1 \
-DWITH_BOOST=/usr/local/boost \
-DENABLE_DOWNLOADS=1
```

