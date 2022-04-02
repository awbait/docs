# Airflow

Скачать мой чарт:
```bash
wget -O - https://github.com/awbait/infrastructure-as-code/archive/master.tar.gz | tar -xz --strip=3 "infrastructure-as-code-master/kubernetes/charts/deploy-airflow"
```

### 1. Преднастройка базы данных

Создаём пользователя и базу данных:
```sql
CREATE USER airflow WITH PASSWORD 'airflow' login;
CREATE DATABASE airflow with owner airflow;
CREATE SCHEMA airflow AUTHORIZATION airflow;
```

### 2. Конфигурация чарта:

Так как репозитория на github'e официального чарта я не нашел. Пришлось скачать все значения чарта через команду (данный файлик уже имеется в моём чарте):

``` bash
helm show values apache-airflow/airflow > values.yaml.example
```

В файле values.yaml можно изменять различные параметры чарта.

#### Настройка подключения к базе данных

В следующих файлах можно изменить строки подключения к внешней базе данных по шаблону:

=== "templates/database-credentials.yaml"

    ``` yaml
    connection: {{ "postgresql://user:pass@host:5432/db?sslmode=disable" | b64enc }}
    ```

=== "templates/database-backend-credentials.yaml"

    ``` yaml
    connection: {{ "db+postgresql://user:pass@host:5432/db?sslmode=disable" | b64enc }}
    ```

#### Секретный ключ

Далее необходимо сгенерировать секретный ключ командой:

``` bash
python3 -c 'import secrets; print(secrets.token_hex(16))' | base64
```

Копируем и заменяем содержимое в файле:
``` yaml title="templates/airflow-webserver-secret.yaml"
webserver-secret-key: YOUR_SECRET_KEY
```

#### Настройка Ingress

В своём примере я использую однонодовый k3s кластер, в котором по умолчанию идет обратный прокси сервер с лоадбалансером - traefik.

Для корректного отображения страницы не на root пути, а на /airflow и /flower мне пришлось создать 2 middleware: ingress-airflow-middleware.yaml и ingress-flower-middleware.yaml

И включить их в Ingress-классах через добавление их в аннотации.

Поэтому если вам это не нужно, отредактируйте файл values.yaml и удалите эти два файла в папке templates.

[traefik-stripprefix.md](../hacks/traefik-stripprefix.md)

### 3. Установка чарта:

!!! note ""
    В данной команде мы используем дополнительных два параметра для корректного деплоя

Устанавливаем чарт:
``` bash
helm install airflow deploy-airflow/ -n airflow --create-namespace --debug --timeout=10m
```

### 4. Обновление зависимостей и чарта

Обновление зависимостей чарта:
``` bash
helm dependency update deploy-airflow/
```

Обновление чарта:
``` bash
helm upgrade airflow deploy-airflow/ -n airflow
```

### 5. Удалить установленный чарт

Удалить установленный чарт:
``` bash
helm delete airflow -n airflow
```
