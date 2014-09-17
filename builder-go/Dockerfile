FROM google/golang

RUN apt-get update && apt-get -y install golang-go

ENV GOPATH /buildenv

WORKDIR /buildenv

ENTRYPOINT ["go", "build", "-v"]
CMD ["."]
