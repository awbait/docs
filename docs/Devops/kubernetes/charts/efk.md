# EFK Stack

Скачать мой чарт:
``` bash
wget -O - https://github.com/awbait/infrastructure-as-code/archive/master.tar.gz | tar -xz --strip=3 "infrastructure-as-code-master/kubernetes/charts/deploy-efk"
```

### 1. Установка чарта

``` bash
helm install efk deploy-efk/ -n efk --create-namespace
```

### 2. Обновление зависимостей и чарта

Обновление зависимостей чарта:
``` bash
helm dependency update deploy-efk/
```
Обновление чарта:
``` bash
helm upgrade efk deploy-efk/ -n efk
```

### 3. Удалить установленный  чарт

Удалить установленный чарт:
``` bash
helm delete efk -n efk
```


### 4. Debug services

Проброс порта до kibana (для теста):
``` bash
kubectl port-forward deployment/efk-kibana 5601 -n efk
```