# Commands

Получить информацию об установленном docker'e:
```
docker info
```

``` bash
docker login
```

``` bash
docker image tag myhello YOUR_DOCKER_ID/myhello
```

``` bash
docker image push YOUR_DOCKER_ID/myhello
```

``` bash
docker container run -p 9999:8888 YOUR_DOCKER_ID/myhello
```

Изменить cgroupdriver в экземпляре docker:

``` json title="/etc/docker/daemon.json"
{ 
  "exec-opts": ["native.cgroupdriver=systemd"] 
}
```
