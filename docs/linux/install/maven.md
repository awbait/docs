# Установка кастомной версии Maven

Скачиваем необходимую версию: https://archive.apache.org/dist/maven/maven-3/
``` bash
wget https://archive.apache.org/dist/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
```

Распаковываем архив в системную директорию:
``` bash
tar xvf apache-maven-3.6.3-bin.tar.gz -C /usr/lib/
```

Редактируем файл профиля для присвоения переменных среды:

В конец файла дописываем и сохраняем:
``` bash title="/etc/profile"
M2_HOME="/usr/lib/apache-maven-3.6.3"
export M2_HOME

M2="$M2_HOME/bin"
MAVEN_OPTS="-Xms256m -Xmx512m"
export M2 MAVEN_OPTS

PATH=$M2:$PATH
export PATH
```

Применить изменения профиля:
``` bash
. /etc/profile
```

Подтвердить переменные
``` bash
env | grep M2
```

Проверка установленной версии:
``` bash
mvn -version
```
