# k3sup

Утилита для быстрого развертывания k3s кластера

!!! github "[GitHub]"
  [github]: https://github.com/alexellis/k3sup

### Загрузка и установка:

``` bash
curl -sLS https://get.k3sup.dev | sh 
sudo install k3sup /usr/local/bin/
```

Проверяем:
``` bash
k3sup --help
```

Создаём папку для конфига:
``` bash
mkdir ~/.kube
```

Установить на локальную машину:
``` bash
k3sup install \
--local \
--merge \
--local-path ~/.kube/config
```
