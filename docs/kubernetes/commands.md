# Commands

Вывести команду для подключения нового узла к кластеру:
``` bash
kubeadm token create --print-join-command
```

Инициализировать кластер:
``` bash
kubeadm init --apiserver-advertise-address=<MASTER_IP> --pod-network-cidr=10.100.0.0/16
```

```
--control-plane-endpoint : set the shared endpoint for all control-plane nodes. Can be DNS/IP
--pod-network-cidr : Used to set a Pod network add-on CIDR
--cri-socket : Use if have more than one container runtime to set runtime socket path
--apiserver-advertise-address : Set advertise address for this particular control-plane node's API server
```

Удалить ноду из кластера:
``` bash
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
kubectl delete node <node-name>
kubeadm reset # на узле который удалили
```

Посмотреть использование ресурсов нод:
``` bash
kubectl describe nodes | awk '/Allocated resources/,/Events/' | head -n-1
```

Посмотреть все что есть в кластере:
``` bash
kubectl get all,cm,secret,ing -A
```

Полная информация о нодах:
``` bash
kubectl describe nodes
```

Откатить внесенные изменения:
``` bash
kubectl reset
```

Назначить метку для ноды:
``` bash
kubectl label nodes <your-node-name> <label>
```

Назначить роль воркер-узлу:
``` bash
kubectl label node <node-name> node-role.kubernetes.io/worker=worker
```

### Namespaces

Создать пространство:
``` bash
kubectl create ns <namespace>
```

Удалить пространство:
``` bash
kubectl delete ns <namespace>
```

Удалить все из namespace:
``` bash
kubectl delete all --all -n <namespace>
```

Обновить зависимости чарта:
``` bash
helm dependency update <chart-name>
```

Изменить пространство:
``` bash
kubectl config set-context --current --namespace=<namespace>
```

Остальное
``` bash
kubectl scale statefulsets elasticsearch-master --replicas=0 -n efk
kubectl -n efk get secret elastic-certificates -o jsonpath='{.data.elastic-certificates\.p12}' | base64 --decode | openssl pkcs12 -info
curl -u elastic:changeme http://elasticsearch-master:9200/_cluster/health?pretty=true
```