# active
观察哪一个 machine 处于"激活状态"（如果  环境变量指向了该 machine 的话，我们就认为这个 machine 已处于激活状态）。

```
$ docker-machine ls
NAME      ACTIVE   DRIVER         STATE     URL
dev                virtualbox     Running   tcp://192.168.99.103:2376
staging   *        digitalocean   Running   tcp://104.236.50.118:2376
$ echo $DOCKER_HOST
tcp://104.236.50.118:2376
$ docker-machine active
staging
```

