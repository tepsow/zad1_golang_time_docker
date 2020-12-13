# go-docker

A simple web app written in Go, displaying time for Warsaw, New York and Sydney
Docker image size 1.64MB
Create project next project structure:
- goapp
 -src/main.go
 -Dockerfile
 
 Insert in main go and save:
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
#Insert in Dockerfile and save :

Build and run from goapp folder:
Access via http://localhost:8080/
```bash
$ docker build  -t goapp .
$ docker run --rm -it -p 8080:8080 goapp
```


