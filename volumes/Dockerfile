#FROM debian:wheezy
# busybox:latest doesn't have the necessary libs to run a go binary so we use busybox:ubuntu-14.04
FROM busybox:ubuntu-14.04

VOLUME ["/test/"]

RUN ls -ahl /test/

# Add the binary from the build phase
# ADD go-hello-world-http /go-hello-world-http

# Expose the port we're serving on
EXPOSE 80

# Run the binary when starting a container from this image
#CMD ["/go-hello-world-http"]
