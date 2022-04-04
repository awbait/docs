# Longhorn

Ansible:

``` bash
ansible-playbook playbooks/kubernetes/longhorn-prep.yml -l cluster_group
```

Создать файл с содержимым:
```
createDefaultDiskLabeledNodes - Включает метки, которые будут определять на какой виртуальной машине можно хранить данные
defaultDataPath - Путь на виртуальных машинах, где будут храниться данные
```

```yaml title="values.yaml"
defaultSettings:
  createDefaultDiskLabeledNodes: true
  defaultDataPath: "/opt/longhorn_data"
```

Прописываем метки для узлов:

``` bash
kubectl label nodes <your-node-name> node.longhorn.io/create-default-disk=true
```

# Установка:

Добавляем репозиторий:
``` bash
helm repo add longhorn https://charts.longhorn.io
```

Устанавливаем чарт:
``` bash
helm upgrade longhorn longhorn/longhorn -f values.yaml -n longhorn-system --install --create-namespace
```
