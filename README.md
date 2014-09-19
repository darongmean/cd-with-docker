# Setup the local repository

- Run all these commmands INSIDE boot2docker:
`boot2docker ssh`

- Export the build directories:
`export SRC=/home/docker/src`
`export BUILDENV=/home/docker/buildenv/src`
`export REPODIR=/home/docker/remote`

- Create GIT repository with commit hook:
```
mkdir $REPODIR
cd $REPODIR
git init --bare
mkdir -p hooks
```

- Create a post-receive hook:
```
cat > hooks/post-receive << \EOF
#!/bin/sh

unset GIT_DIR

if [ ! -d $BUILDENV ]; then
    git clone $REPODIR $BUILDENV
else
    cd $BUILDENV && git pull
fi
EOF
chmod +x hooks/post-receive
```

- Clone the GO Hello World application
```
git clone https://github.com/simonvanderveldt/go-hello-world-http $SRC
cd $SRC
```

- Push to the "local remote branch"
```
git remote add remote $REPODIR
git push remote master
```

- Your code should be visible in directory `$BUILDENV` and is ready for the build phase.

# Setup

From the root of this repository:

- Build the buildenv image: `docker build -t builder builder/`
- Build the buildenv-go image: `docker build -t builder-go builder-go/`


# Build a Go artifact
- Clone the repo to build to /home/docker/buildenv/src: `git clone https://github.com/simonvanderveldt/go-hello-world-http.git /home/docker/buildenv/src`
- Run the buildenv-go container with `/home/docker/buildenv` mounted and the name of the package to build: `docker run --rm -v /home/docker/buildenv:/buildenv buildenv-go go build -v go-hello-world-http`
  - The compiled binaries are available at `/home/docker/buildenv`

# Run the runner container
- Create the runner container: `docker build -t runner runner/`
- Run the runner container: `docker run -d --name runner -v $BUILDENV:/buildenv -p 80:80 runner`
