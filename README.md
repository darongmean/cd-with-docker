# Setup

- Build the buildenv image: `docker build -t buildenv buildenv/`
- Build the buildenv-go image: `docker build -t buildenv-go buildenv-go/`


# Build a Go artifact
- Clone the repo to build to /home/docker/buildenv/src: `git clone https://github.com/simonvanderveldt/go-hello-world-http.git /home/docker/buildenv/src`
- Run the buildenv-go container with `/home/docker/buildenv` mounted and the name of the package to build: `docker run --rm -v /home/docker/buildenv:/buildenv buildenv-go go-hello-world-http`
  - The compiled binaries are available at `/home/docker/buildenv`
