# Установка ADB

### Загрузка бандла ADB
Загрузка ADB-Bundle: https://store.arenadata.io/#products/arenadata_db


Заходим в BUNDLES и загружаем скачанный бандл

Нажимаем на желтый треугольник с воскл. знаком и соглашаемся с лицензией

### Создание кластера ADB

Переходим в CLUSTERS и создаем кластер с нашим бандлом

### Создать нового пользователя
Создаем пользователя в системе:

``` bash
adduser USERNAME
```

Авторизуемся под пользователем:
``` bash
su - USERNAME
```

Далее необходимо отредактировать файл `.bashrc`
```
vim ~/.bashrc

Добавляем в конец:
source /usr/lib/gpdb/greenplum_path.sh
export MASTER_DATA_DIRECTORY=/data1/master/gpseg-1
export PXF_CONF="/var/lib/pxf/conf"
export PXF_HOME="/var/lib/pxf"
```


