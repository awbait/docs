# Установка ADCM

### Загрузка образа контейнера:

``` bash
docker pull hub.arenadata.io/adcm/adcm:latest
```

### Создание контейнера:

``` bash
docker create --name adcm -p 8000:8000 -v /opt/adcm:/adcm/data hub.arenadata.io/adcm/adcm:latest
```

### Запуск и остановка контейнера:

``` bash
docker start adcm
```

После запуска будет веб-интерфейс будет доступен по адресу: `http://<ip_address_of_server>:8000`

!!! abstract "Доступы"
    Login/Password:
    admin/admin

``` bash
docker stop adcm
```

### Загрузка SSH Common Bundle
Для дальшейшей работы с ADCM необходимо загрузить и установить SSH Common Bundle, для управления хостами через SSH

Загрузить можно тут: https://store.arenadata.io/#products/arenadata_cluster_manager на вкладке Arenadata ADCM Infrastructure Bundles

Установить его в систему можно на вкладке BUNDLE

Далее переходим в HOSTPROVIDER и создаем провайдер

### Создание SSH ключа

mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys

chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys

vim ~/.ssh/authorized_keys

Сгенерировать ключ:
ssh-keygen -t rsa

ssh-copy-id root@server