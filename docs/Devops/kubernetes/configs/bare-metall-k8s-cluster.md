# bare-metall k8s cluster

### Инициализация кластера:

Инициализируем на master-узле:
``` bash
kubeadm init --apiserver-advertise-address=<MASTER_IP> --pod-network-cidr=10.100.0.0/16
```

По выполнению команды, получим команду в виде:

`kubeadm join <MASTER\_IP>:<PORT> --token 38i2yk.nsges1b5btqnzk4j4 \
--discovery-token-ca-cert-hash sha256:6e84b5587af506009db7ab07de8142d5e6869b0186d932dd729601d2729a880f`

Скопируем и сохраняем, она нам понадобится дальше

Так же на master-узле прописываем команды:

``` bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Устанавливаем Network-plugin:

Скачиваем конфиг:
``` bash
curl https://projectcalico.docs.tigera.io/manifests/calico.yaml -O
```

Редактируем конфигурационный файл, находим строчки указанные ниже раскомментируем их и заменяем пул ip-адресов на тот который указывали ранее при инициализации кластера в параметре (pod-network-cidr)

```yaml title="calico.yaml"
- name: CALICO_IPV4POOL_CIDR 
  value: "10.100.0.0/16"
```

Применить конфиг:
``` bash
kubectl apply -f calico.yaml
```

### Подключаем worker-узлы

Можно использовать команду которую мы получили в первом шаге.

Или можем запросить её повторно:
``` bash
kubeadm token create --print-join-command
```

При возникновении ошибки с bridge-nf-call-iptables вводим команду:

``` bash
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```

Назначаем роль worker-узлу:
``` bash
kubectl label node <HOSTNAME_WORKER> node-role.kubernetes.io/worker=worker
```
