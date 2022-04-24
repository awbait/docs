# rsyslog

Для получения основного сообщения используем:

```
%msg%\n
# Чтобы убрать пробел перед сообщением используем усложненную версию:
%msg:2:$%\n
```

Изменение формата получения записей:

```
# Актуальный на rsyslog 8.x:
$template noFormat,"%msg:2:$%\n"
module(load="builtin:omfile" Template="noFormat")
# Старые варианты:
$template noFormat,"%msg%\n"
$ActionFileDefaultTemplate noFormat
template (name="sample_template" type="string" string="%timegenerated% %HOSTNAME% %syslogfacility-text%.%syslogseverity-text% %syslogtag% %msg%\n")
$ActionFileDefaultTemplate sample_template
```

Получение файлов и сохранять их в определенную папку (данный фрагмент должен находится в разделе правил (Rules) иначе то же форматирование может не работать должным образом):

```
$template RemoteLogs,"/var/log/rsyslog/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?RemoteLogs
& ~
```