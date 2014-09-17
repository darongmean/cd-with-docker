# Setup the local repository

- Run all these commmands INSIDE boot2docker:
`boot2docker ssh`

- Export the build directories:
`export BUILDENV=/home/docker/buildenv/src`
`export REPO=/home/docker/remote`

- Create GIT repository with commit hook:
```
cd $REPO
git init --bare
mkdir hooks
```

- Create a post-receive hook:
```
cat > hooks/post-receive << EOF
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
cd $BUILDENV
git clone https://github.com/simonvanderveldt/go-hello-world-http 
cd go-hello-world-http
```

- Push to the "local remote branch"
```
git remote add remote ../remote
git push remote master
```

# Setup

- Build the buildenv image: `docker build -t buildenv buildenv/`
- Build the buildenv-go image: `docker build -t buildenv-go buildenv-go/`


# Build a Go artifact
- Clone the repo to build to /home/docker/buildenv/src: `git clone https://github.com/simonvanderveldt/go-hello-world-http.git /home/docker/buildenv/src`
- Run the buildenv-go container with `/home/docker/buildenv` mounted and the name of the package to build: `docker run --rm -v /home/docker/buildenv:/buildenv buildenv-go go-hello-world-http`
  - The compiled binaries are available at `/home/docker/buildenv`
