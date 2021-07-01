# Builds the static Go image to execute in a Kubernetes job

FROM golang:1.15
ARG GOARCH=amd64
WORKDIR /go/src/github.com/GoogleContainerTools/kaniko

RUN echo $GOARCH > /goarch

#This arg is passed by docker buildx & contains the platform info in the form linux/amd64, linux/ppc64le etc.
ARG TARGETPLATFORM

#Capture ARCH has write to /goarch
RUN [ ! "x" = "x$TARGETPLATFORM" ] && `echo $TARGETPLATFORM |  awk '{split($0,a,"/"); print a[2]}' > /goarch` || echo "$GOARCH"

# Add .docker config dir
RUN mkdir -p /kaniko/.docker

COPY . .
RUN make GOARCH=$(cat /goarch.txt)

# Generate latest ca-certificates

FROM debian:buster-slim AS certs

RUN \
  apt update && \
  apt install -y ca-certificates && \
  cat /etc/ssl/certs/* > /ca-certificates.crt

FROM alpine:3.13

COPY --from=0 /go/src/github.com/GoogleContainerTools/kaniko/out/executor /kaniko/executor
COPY --from=certs /ca-certificates.crt /kaniko/ssl/certs/
COPY --from=0 /kaniko/.docker /kaniko/.docker
COPY files/nsswitch.conf /etc/nsswitch.conf
ENV HOME /root
ENV USER root
ENV PATH /bin:/usr/local/bin:/kaniko
ENV SSL_CERT_DIR=/kaniko/ssl/certs
ENV DOCKER_CONFIG /kaniko/.docker/
