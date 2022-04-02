# External Nginx Ingress

!!! github "Исходный чарт [GitHub]"
  [github]: https://github.com/kubernetes/ingress-nginx

### 1. Установка чарта

``` bash
helm install ingress-nginx deploy-external-nginx-ingress/ -n ingress-nginx --create-namespace
```

### 2. Использование

Для использования данного Ingress Controller'a используем поле **ingressClassName** в Ingress классах, чтобы передавать управление ими этому контроллеру.

### 3. Обновление зависимостей и чарта

Обновление зависимостей чарта:
``` bash
helm dependency update deploy-external-nginx-ingress/
```
Обновление чарта:
``` bash
helm upgrade ingress-nginx deploy-external-nginx-ingress/ -n ingress-nginx
```

### 4. Удалить установленный  чарт

Удалить установленный чарт:
``` bash
helm delete ingress-nginx -n ingress-nginx
```