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
