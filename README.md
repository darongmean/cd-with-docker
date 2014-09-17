# Build the binary
`docker run --rm buildenv-go:latest sh -c 'go get github.com/simonvanderveldt/go-hello-world-http && cat /usr/lib/go/bin/go-hello-world-http' > go-hello-world-http`

`--rm` deletes the container after it exits
`sh -c` is necessary to make the shell within the container eval the combination of build & cat
`> go-hello-world-http` save the cat output from the container in `go-hello-world-http`

# Build the container
docker build -t go-hello-world-http:latest go-hello-world-http
