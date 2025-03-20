```shell
$ docker run -d -p 5000:5000 --restart=always --name registry registry
$ docker tag ubuntu:latest 127.0.0.1:5000/ubuntu:latest
$ docker image ls
$ docker push 127.0.0.1:5000/ubuntu:latest
$ curl 127.0.0.1:5000/v2/_catalog
```