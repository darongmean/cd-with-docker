# Continuous Delivery Pipeline
![Continuous Deployment Pipeline](img/continuous-deployment-pipeline.png) <!-- .element: class="noborder" -->

!SLIDE
![Docker logo](img/docker-logo.png) <!-- .element: class="noborder" -->

!SUB
## Docker introduction
A portable, lightweight application runtime and packaging tool.

_[docker.com](https://www.docker.com)_

!SUB
## Docker features

- Docker engine
- Dockerfiles
- Docker hub


!SLIDE
# Docker advantages
for
# Continuous Delivery

!SUB
## Faster
- Containers are fast!
- Slow one-time event happen only once on image creation not on instance creation

!NOTE
One-time example initialisation of the app has to happen just once. Fort example for test and production, same artifact is started which had it's initialization done @ build time

!SUB
## Better
- Isolation
- Scalability
- Consistent/reproducible results
- Portable/host-independent
- Infrastructure as code
- Immutable infrastructure
- Chaos monkey/gorilla

!SUB
## Cheaper
- Less overhead


!SLIDE
# Continuous Delivery Pipeline
with

![Docker logo](img/docker-logo-no-text.png) <!-- .element: class="noborder" -->


!SLIDE
## Code
- Develop
- Commit
- Post-commit hook triggers new "delivery"


!SLIDE
## Build
- Get sources
- Compile sources <span class="fragment">in `builder` container</span>
- The container image is the artifact <!-- .element: class="fragment" -->

!SUB
### First build
```bash
docker run -ti google/golang bash
```
inside the container:
```bash
cd /gopath
git clone https://github.com/simonvanderveldt/go-hello-world-http /gopath/src
go build go-hello-world-http
exit
```

!SUB
### Create and run image
```bash
docker ps -l
docker commit {CONTAINER ID} go-hello-world-http
docker images #go-hello-world-http image is visible
docker run -d -p 80:80 go-hello-world-http /gopath/go-hello-world-http
```

!SUB
### Does it work?
```bash
curl localhost
> Hello, world!
```

```bash  
# Stop the container
docker kill {CONTAINER ID}
```

!SUB
### Check
What have we done thus far?

What can we improve? <!-- .element: class="fragment" -->

!SUB
### Build using Dockerfile
`go-hello-world-http/Dockerfile`
```dockerfile
FROM google/golang

ENV GOPATH /gopath

WORKDIR /gopath

RUN git clone https://github.com/simonvanderveldt/go-hello-world-http /gopath/src

RUN go build go-hello-world-http
```

!SUB
### Build and run image
```bash
docker build -t go-hello-world-http ./go-hello-world-http
docker run -d -p 80:80 go-hello-world-http /gopath/go-hello-world-http
```

!SUB
### Check
What can we improve? 
```
docker images | grep go-hello-world-http
> go-hello-world-http latest d31a90b28d50 2 minutes ago 565.3 MB
```

!SUB
### Getting rid of our build-time tools
We don't need them during run-time


Solution: 2 Dockerfiles <!-- .element: class="fragment" -->

- Generic builder <!-- .element: class="fragment" -->
- Application <!-- .element: class="fragment" -->

!SUB
### Generic builder
`builder/Dockerfile`
```dockerfile
FROM google/golang

ENV GOPATH /gopath

WORKDIR /gopath

ENTRYPOINT ["go", "build"]

CMD ["."]
```

```
docker build -t builder ./builder
```

!SUB
### Build the application
```bash
git clone https://github.com/simonvanderveldt/go-hello-world-http /home/docker/cd-with-docker/go-hello-world-http-v2/src
docker run --rm --volume /home/docker/cd-with-docker/go-hello-world-http-v2/:/gopath builder go-hello-world-http
```
Build artifact is now available at

`/home/docker/cd-with-docker/go-hello-world-http-v2`

!SUB
### Application
`go-hello-world-http-v2/Dockerfile`
```dockerfile
FROM busybox:ubuntu-14.04

EXPOSE 80

ADD go-hello-world-http /go-hello-world-http

ENTRYPOINT /go-hello-world-http 
```
```bash
docker build -t go-hello-world-http-v2 ./go-hello-world-http-v2
docker run -d -p 80:80 --name go-hello-world-http-v2 go-hello-world-http-v2
```

!SUB
### Result
```
docker images | grep hello-world-http-v2
> go-hello-world-http-v2 latest 903b479cd26c 2 minutes ago 11.3 MB
```

!SLIDE
## Test
- Run tests <span class="fragment">from `tester` container</span>
- Artifact container is the System Under Test <!-- .element: class="fragment" -->

!SUB
### Build tester
`tester/Dockerfile`
```dockerfile
FROM google/golang

RUN apt-get update && apt-get install -y curl

ADD test.sh /test.sh

RUN chmod +x /test.sh

CMD /test.sh http://$SUT_PORT_80_TCP_ADDR:$SUT_PORT_80_TCP_PORT
```
```bash
docker build -t tester ./tester/
```

!SUB
### Run tests
```bash
docker run --link go-hello-world-http-v2:sut tester 
```

!SUB
### Test result
The test fails :(

Make the test pass!

!SLIDE
## Deploy
- Deploy the artifact<span class="fragment"> (container image) to the Docker Hub</span>

!SUB
### Optional: push to Docker Hub
```
docker login
docker push {DOCKER_USERNAME}/go-hello-world-http-v2
```
