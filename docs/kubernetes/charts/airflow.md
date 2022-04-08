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

### 2. Конфигурация чарта: {: #conf }

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

### 6. Настройка GitSync

#### Создание приватного репозитория и настройка подключения к нему

Сначала создаем приватный репозиторий. Далее необходимо сгенерировать SSH ключ чтобы настроить подключение к нашему репозиторию по SSH.

``` bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

##### Добавляем публичный ключ к нашему репозиторию

Ключ обычно находится по пути: `~/.ssh/id_rsa.pub`

Выводим на экран и копируем:
``` bash
cat ~/.ssh/id_rsa.pub
```

Я использую GitLab поэтому открываю репозиторий -> Settings -> Repository -> Deploy Keys и добавляю наш ключ.

Убедитесь что галочка "для записи" установлена.

##### Редактируем и создаём secret в Kubernetes

Аналогичным методом копируем приватный ключ из файла `~/.ssh/id_rsa` командой:

``` bash
cat ~/.ssh/id_rsa | base64
```

Копируем содержимое и вставляем в файл:

``` yaml title="templates/airflow-ssh-secret.yaml"
gitSshKey: <YOUR_PRIVATE_KEY_IN_BASE64>
```

##### Редактируем конфигурацию чарта

``` yaml title="values.yaml"
dags:
  gitSync:
    enabled: true
    repo: ssh://git@domain.ru/progect-name/repo-name.git
    branch: main
    rev: HEAD
    maxFailures: 0
    subPath: "dags"
    sshKeySecret: airflow-ssh-secret
```

Обновляем чарт из п.4

Готово.

#### 7. Дополнительно #### {: #other }

Для взаимодействия с Camunda необходимо установить переменную:

``` yaml
env:
  - name: "AIRFLOW__API__AUTH_BACKEND"
    value: "airflow.api.auth.backend.basic_auth"
```
Документация параметра: [https://airflow.apache.org/docs/apache-airflow/stable/security/api.html](https://airflow.apache.org/docs/apache-airflow/stable/security/api.html)
