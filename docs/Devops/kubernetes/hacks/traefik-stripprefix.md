# Traefik - stripPrefix

Как удалить префикс из пути перед отправкой запроса. Очень распространенная проблема.

!!! info "[Официальная документация]"
  [Официальная документация]: https://doc.traefik.io/traefik/middlewares/http/stripprefix

Пример: Если ваш сервис слушает / рут путь, но вы хотите его свесить на /name через Ingress при использовании Traefik, то вам необходимо сделать:

#### 1. Необходимо создать Middleware

``` yaml
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: airflow-stripprefix
  namespace: airflow
spec:
  stripPrefix:
    prefixes:
      - /airflow
```

#### 2. Добавить аннотацию Ingress-классу

Будьте внимательны с именем аннотации оно формируется по такому шаблону:

`namespace-middleware_name@kubernetescrd`
``` yaml
traefik.ingress.kubernetes.io/router.middlewares: airflow-airflow-stripprefix@kubernetescrd
```

Так же необходимо проверить что **path** у класса Ingress такой же, какой вы удаляете в middleware.&#x20;

#### 3. Готово