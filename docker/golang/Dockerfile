FROM ubuntu:trusty
MAINTAINER https://github.com/cloudfoundry-incubator/cf-routing-release

ENV GO_VERSION 1.10.2

# Install go
RUN curl https://storage.googleapis.com/golang/go${GO_VERSION}.linux-amd64.tar.gz | tar xvz -C /usr/local/

# Add golang environment variables
ENV HOME /root
ENV GOPATH /root/go
ENV PATH $PATH:/usr/local/go/bin:/root/bin:$GOPATH/bin

# install common CI dependencies
RUN go get \
  golang.org/x/tools/cmd/cover

# install glide
RUN add-apt-repository ppa:masterminds/glide && \
  apt-get update && \
  apt-get -y install glide && \
  apt-get clean

# install go dep
RUN go get \
  -u github.com/golang/dep/cmd/dep

# install common CI dependencies
RUN go get \
  golang.org/x/tools/cmd/cover

