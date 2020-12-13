# go-docker

A simple web app written in Go, displaying time for Warsaw, New York and Sydney
# Docker image size 1.64MB
# Create next project structure:
```
- goapp/
   -src/main.go
   -Dockerfile
```
 Insert into main.go and save:
 ```
 package main

import (
	"fmt"
	"net/http"
	"os"
	"time"
	"log"
)

func printTime(w http.ResponseWriter, r *http.Request)  {

    utc := time.Now().UTC()
    local := utc
	
    location, err := time.LoadLocation("Europe/Warsaw")
    if err == nil {
        local = local.In(location)
        fmt.Fprint(w, local.Location(), " ", local.Format("2 Jan 2006 15:04"), "\n")
    }
    
    local = utc
    location, err = time.LoadLocation("America/New_York")
    if err == nil {
        local = local.In(location)
        fmt.Fprint(w, local.Location(), " ", local.Format("2 Jan 2006 15:04"), "\n")
    }
    
    local = utc	
    location, err = time.LoadLocation("Australia/Sydney")
    if err == nil {
        local = local.In(location)
        fmt.Fprint(w, local.Location(), " ", local.Format("2 Jan 2006 15:04"))
    }
	
      
	
    
 
    
}



func main() {
  
  
	http.HandleFunc("/", printTime)

	// get port env var
	port := "8080"
	portEnv := os.Getenv("PORT")
	if len(portEnv) > 0 {
		port = portEnv
	}

	log.Printf("Listening on port %s...", port)
	log.Fatal(http.ListenAndServe(fmt.Sprintf(":%s", port), nil))
	// listen and serve on 0.0.0.0:8080 by default
	// set environment variable PORT if you want to change port
}
 ```
#Insert into Dockerfile and save :

```
FROM golang:latest as builder
# install xz
RUN apt-get update && apt-get install -y \
    xz-utils \
&& rm -rf /var/lib/apt/lists/*
# install UPX
ADD https://github.com/upx/upx/releases/download/v3.94/upx-3.94-amd64_linux.tar.xz /usr/local

RUN xz -d -c /usr/local/upx-3.94-amd64_linux.tar.xz | \
    tar -xOf - upx-3.94-amd64_linux/upx > /bin/upx && \
    chmod a+x /bin/upx
# install dep
RUN go get github.com/golang/dep/cmd/dep
# create a working directory
WORKDIR /go/src/app
RUN dep init
# install packages
RUN dep ensure --vendor-only
# add source code
ADD src src
RUN git clone https://github.com/tepsow/timeforgolang_3city.git 



# build the source
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main src/main.go
# strip and compress the binary
RUN strip --strip-unneeded main
RUN upx main

# use scratch (base for a docker image)
FROM scratch
# set working directory
WORKDIR /root
# copy the binary from builder
COPY --from=builder /go/src/app/main .

COPY --from=builder /go/src/app/timeforgolang_3city/time/ .
ENV ZONEINFO .

# run the binary
CMD ["./main"]
```

Build and run from goapp folder:
Access via http://localhost:8080/
```bash
$ docker build  -t goapp .
$ docker run --rm -it -p 8080:8080 goapp
```


