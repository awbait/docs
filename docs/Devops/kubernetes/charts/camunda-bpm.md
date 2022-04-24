# Camunda BPM

!!! github "Исходный чарт [GitHub]"
  [github]: https://github.com/camunda-community-hub/camunda-helm/tree/main/charts/camunda-bpm-platform

Скачать мой чарт:
``` bash
wget -O - https://github.com/awbait/infrastructure-as-code/archive/master.tar.gz | tar -xz --strip=3 "infrastructure-as-code-master/kubernetes/charts/deploy-camunda"
```

### 1. Преднастройка базы данных

!!! warning
    Не получилось запустить на Postgresql 14.2.0, заработало только на 13.6.0

Создаём пользователя и базу данных:
``` sql
CREATE DATABASE camunda;
CREATE USER camunda WITH PASSWORD 'camunda$4';
GRANT ALL PRIVILEGES ON DATABASE camunda to camunda;
```

### 2. Конфигурация чарта:

В файле values.yaml можно изменить различные параметры, в том числе и строка для подключения к базе данных.


```yaml title="values.yaml"
camunda-bpm-platform:
  general:
    debug: false
  database:
    driver: org.postgresql.Driver
    url: jdbc:postgresql://postgresql.postgresql:5432/camunda
       # jdbc:postgresql://<HOSTNAME>:<PORT>/<DATABASE>
```

В следующем файле можно изменить данные (Логин/Пароль) для подключения к базе данных

``` yaml title="templates/database-credentials.yaml"
DB_USERNAME: {{ "camunda" | b64enc }}
DB_PASSWORD: {{ "camunda$4" | b64enc }}
```

### 3. Установка чарта:

Устанавливаем чарт:
``` bash
helm install camunda deploy-camunda/ -n camunda --create-namespace
```

### 4. Обновление зависимостей и чарта

Обновление зависимостей чарта:
``` bash
helm dependency update deploy-camunda/
```

Обновление чарта:
``` bash
helm upgrade camunda deploy-camunda/ -n camunda
```

### 5. Удалить установленный  чарт

Удалить установленный чарт:
``` bash
helm delete camunda -n camunda
```
