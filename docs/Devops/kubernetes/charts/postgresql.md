# Postgresql

!!! github "Исходный чарт [GitHub]"
  [github]: https://github.com/bitnami/charts/tree/master/bitnami/postgresql

Скачать мой чарт:
``` bash
wget -O - https://github.com/awbait/infrastructure-as-code/archive/master.tar.gz | tar -xz --strip=3 "infrastructure-as-code-master/kubernetes/charts/deploy-postgresql"
```

### 1. Установка чарта

``` bash
helm install postgresql deploy-postgresql/ -n postgresql --create-namespace
```

### 2. Подключение к базе данных:

В данных командах используется default пространство!

Получить и записать пароль в переменную:
``` bash
export POSTGRES_PASSWORD=$(kubectl get secret --namespace postgresql postgresql -o jsonpath="{.data.postgres-password}" | base64 --decode)
```
Запускаем клиент и подключаемся:
``` bash
kubectl run postgresql-client --rm --tty -i --restart='Never' --namespace postgresql --image docker.io/bitnami/postgresql:14.2.0-debian-10-r35 --env="PGPASSWORD=$POSTGRES_PASSWORD" \
--command -- psql --host postgresql -U postgres -d postgres -p 5432
```

### 3. Обновление зависимостей и установленного чарта:

``` bash
helm dependency update deploy-postgresql/
```

``` bash
helm upgrade postgresql deploy-postgresql/ -n postgresql
```

### 4. Удалить чарт:

Удалить чарт:
```bash
helm delete postgresql -n postgresql
```
