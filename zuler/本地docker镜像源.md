```shell
$ docker run -d -p 17777:17777 --restart=always --name registry registry
$ docker tag ubuntu:latest 127.0.0.1:5000/ubuntu:latest
$ docker image ls
$ docker push 127.0.0.1:5000/ubuntu:latest
$ curl 127.0.0.1:5000/v2/_catalog
```