# TODO Check if smaller baseimage is available
FROM debian:wheezy

ADD /home/docker/buildenv/go-hello-world-http /go-hello-world-http

RUN chmod +x /go-hello-world-http

ENTRYPOINT /go-hello-world-http
