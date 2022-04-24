# MetalLB

Скачать мой чарт:
``` bash
wget -O - https://github.com/awbait/infrastructure-as-code/archive/master.tar.gz | tar -xz --strip=3 "infrastructure-as-code-master/kubernetes/charts/deploy-metallb"
```


### 1. Установка чарта

``` bash
helm install metallb deploy-metallb/ -n metallb --create-namespace
```

### 2. Конфигурация чарта

Чтобы использовать один IP для нескольких сервисов, необходимо указать аннотацию для сервиса:

``` yaml
annotations:
  metallb.universe.tf/allow-shared-ip: "key-to-share-1.2.3.4"
```

В файле values.yaml необходимо прописать пул адресов вида:
```
192.168.1.100/32
192.168.1.1/24
192.168.1.100-192.168.1.150
```

### 3. Обновление зависимостей и установленного чарта:

``` bash
helm dependency update deploy-metallb/
```

``` bash
helm upgrade metallb deploy-metallb/ -n metallb
```

### 4. Удалить чарт:

Удалить чарт:
``` bash
helm delete metallb -n metallb
```
