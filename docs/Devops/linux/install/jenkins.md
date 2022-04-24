# Установка Jenkins

1. Подключаем репозиторий
``` bash
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
```

2. Обновляем репозитории и устанавливаем Jenkins и зависимости
``` bash
yum upgrade -y
yum install epel-release java-11-openjdk-devel -y
yum install jenkins -y
```

3. Если необходимы правки конфига, найти можно тут:
`/etc/sysconfig/jenkins`

4. Перезапускаем демон и запускаем
``` bash
systemctl daemon-reload
systemctl start jenkins
systemctl status jenkins
systemctl enable jenkins
```

Для удобства, чтобы изменить URL интерфейса, открываем конфиг и изменяем строку:

``` bash title="/etc/sysconfig/jenkins"
JENKINS_ARGS="--prefix=/jenkins"
```