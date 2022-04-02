# Fluend

Документация по развертываниям:

!!! github "[fluentd-kubernetes-daemonset]"
  [fluentd-kubernetes-daemonset]: https://github.com/fluent/fluentd-kubernetes-daemonset

!!! github "Чарт [fluentd]"
  [fluentd]: https://github.com/fluent/helm-charts/tree/main/charts/fluentd

Для тестирования конфигурации удобно использовать UI версию:

Билдим контейнер:
``` bash
git clone https://github.com/fluent/fluentd-ui
cd fluentd-ui
docker build -t fluent/fluentd-ui:1.0.0 .
```

В последней версии, я заметил, что у контейнера загружается CPU под потолок, и ВМка падает. Будьте внимательны. Потестили - лучше офнуть контейнер.

Запускаем:
``` bash
docker run -d -v /var/log/fluent:/var/log -p 9292:9292 --name fluentui fluent/fluentd-ui:1.0.0
```

Выключить:
``` bash
docker stop fluentui
```

Зайти в контейнер:
``` bash
docker exec -it fluentui /bin/bash
```

Используемые плагины:

!!! github "[fluent-plugin-multi-format-parser]"
  [fluent-plugin-multi-format-parser]: https://github.com/repeatedly/fluent-plugin-multi-format-parser

Установка:
``` bash
fluent-gem install fluent-plugin-multi-format-parser
```

``` xml
<source>
  @type tail
  path /var/log/atlassian.log
  pos_file /var/log/atlassian.log.pos
  tag atlassian.access
  multiline_flush_interval 5s
  <parse>
    @type multiline
	format_firstline /\d{4}-\d{1,2}-\d{1,2} \d{1,2}:\d{1,2}:\d{1,2},\d{1,3}/
    format1 /^(?<time>\d{4}-\d{1,2}-\d{1,2} \d{1,2}:\d{1,2}:\d{1,2},\d{1,3})\s+(?<msg>.*)/
  </parse>
</source>
<filter atlassian.*>
	@type parser
	key_name msg
	<parse>
		@type multi_format
		<pattern>
        	format regexp
      		expression /^(?<module>\S+)\s+(?<level>\S+)\s+(?<service>\S+)\s+\[(?<class>.*?)\]\s+(?<msg>.*)/
    	</pattern>
        <pattern>
        	format regexp
      		expression /^(?<module>\S+)\s+(?<level>\S+)\s+(?<user>\S+)\s+[\w\s]+\s+[\w\s]+\s+(?<ip_address>[^,]+),[^,\ ]+\s+(?<url>\S+)\s+\[(?<class>.*?)\]\s+(?<message>.*)/
    	</pattern>
	</parse>
</filter>

<match atlassian.*>
  @type stdout
</match>
```
