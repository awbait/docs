# Firewall

Добавить порт в исключения firewall'a:

``` bash
firewall-cmd --permanent --add-port=PORT/tcp
```

Перезапустить firewall:

``` bash
firewall-cmd --reload
```