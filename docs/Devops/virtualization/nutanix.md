# Nutanix

Посмотреть информацию о хостах:

``` bash
ncli host ls
```

Проверить кластер:

``` bash
cluster status
```

### Удалить зависшие задачи:

[https://hyperhci.com/2020/02/14/how-to-kill-nutanix-stuck-hung-task-via-command/](https://hyperhci.com/2020/02/14/how-to-kill-nutanix-stuck-hung-task-via-command/)

Получить список задач:
``` bash
ecli task.list include_completed=false
```

Отменить задачу:
``` bash
ergon_update_task --task_uuid='<Task UUID>' --task_status=aborted
или
ergon_update_task --task_uuid='<Task UUID>' --task_status=succeeded
```

### Изменить имя хоста AHV (Nutanix Change AHV hostname)

``` bash
cd /tmp
```

Загрузить файл
update_hostname_el6el7_v2.sh

Запустить команду с новым именем:
``` bash
bash update_hostname_el6el7_v2.sh new_hostname
```

Авторизоваться по SSH на CVM работающую на этом хосте и выполнить команду:

``` bash
genesis stop acropolis; cluster start
```

### Other command:

``` bash
cluster status
ncc health_checks run_all
ncc health_checks hardware_checks disk_checks sata_dom_status_check
ncli host list
ncli cluster version
virsh list –all
ncc health_checks run_all
ncc health_checks system_checks cluster_connectivity_status
cluster status | grep -v UP
allssh "genesis status | grep '\[\]'"
allssh "ls -ltrhS ~/data/logs/*FATAL*"
```
