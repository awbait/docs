# Установка TeamCity

## Установка

Устанавливаем JAVA
``` bash
yum install java-openjdk -y
```

Скачиваем TeamCity
``` bash
cd /opt
wget https://download.jetbrains.com/teamcity/TeamCity-2021.1.4.tar.gz
```

Распаковываем
``` bash
tar -xzf TeamCity-2021.1.4.tar.gz
```

Создаем пользователя и передаём права на папки
``` bash
useradd -m teamcity
chown -R teamcity /opt/TeamCity
mkdir /opt/.BuildServer
chown -R teamcity /opt/.BuildServer
```

Настраиваем сервис
``` bash
touch /etc/systemd/system/teamcity.service
vim /etc/systemd/system/teamcity.service
```

Наполняем содержимым:
``` bash title="/etc/systemd/system/teamcity.service"
[Unit]
Description=Startup script for TeamCity
After=network.target

[Service]
Type=forking
User=teamcity
ExecStart=/opt/TeamCity/bin/teamcity-server.sh start
ExecStop=/opt/TeamCity/bin/teamcity-server.sh stop

[Install]
WantedBy=multi-user.target
```

Перезапускаем демон и запускаем сервис
``` bash
systemctl daemon-reload
systemctl start teamcity
```

Устанавливаем nginx
``` bash
yum install epel-release -y
yum install nginx -y
touch /etc/nginx/conf.d/teamcity.conf
vim /etc/nginx/conf.d/teamcity.conf
```

Наполняем содержимым
``` bash title="/etc/nginx/conf.d/teamcity.conf"
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''   '';
}

server {
    listen   80;
    server_name  192.168.233.71; # Ваш IP

    proxy_read_timeout     1200;
    proxy_connect_timeout  240;
    client_max_body_size   0;

    location / {
        proxy_pass          http://localhost:8111/;
        proxy_http_version  1.1;
        proxy_set_header    X-Forwarded-For $remote_addr;
        proxy_set_header    Host $server_name:$server_port;
        proxy_set_header    Upgrade $http_upgrade;
        proxy_set_header    Connection $connection_upgrade;
    }
}
```
Проверяем конфиг на корректность и запускаем сервис
``` bash
nginx -t
systemctl start nginx
systemctl enable nginx
```

Открываем в браузере: http://192.168.233.71/mnt

В открытом окне указываем директорию: `/opt/.BuildServer` и жмем Proceed

Далее подключаем базу данных. В этом гайде мы её не настраиваем. Предполагается что у вас имеется любая СУБД. Поэтому просто подключаем её.

Далее создаём пользователя.

Готово.

## Дополнительно

Для удобства можно настроить доступ к TC по `http://192.168.233.71/teamcity`

Останавливаем сервис, переименовываем папку и запускаем. Редактируем строку в конфиге nginx.
``` bash
systemctl stop teamcity
mv /opt/TeamCity/webapps/ROOT /opt/TeamCity/webapps/teamcity
systemctl start teamcity
vim /etc/nginx/conf.d/teamcity.conf
```

``` bash title="/etc/nginx/conf.d/teamcity.conf"
proxy_pass http://localhost:8111/teamcity;
```
