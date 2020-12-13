# go-docker

A simple web app written in Go, displaying time for Warsaw, New York and Sydney
Docker image size 1.96MB

Build and run using any dockerfile:
Access via http://localhost:8080/
```bash
$ docker build  -t goapp .
$ docker run --rm -it -p 8080:8080 goapp
```


