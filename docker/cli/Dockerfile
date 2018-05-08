FROM ubuntu:trusty
MAINTAINER https://github.com/cloudfoundry-incubator/cf-routing-release

ENV BOSH_CLI_VERSION 2.0.48
ENV TERRAFORM_VERSION 0.11.4
ENV BBL_VERSION 6.4.3
ENV ROUTING_API_CLI_VERSION 2.9.0
ENV CF_CLI_VERSION 6.34.1

RUN \
  curl -sL "https://packages.cloudfoundry.org/stable?release=linux64-binary&version=${CF_CLI_VERSION}&source=github-rel" | tar zxf - -C /usr/bin

RUN \
  wget https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip -P /tmp && \
  unzip /tmp/terraform_${TERRAFORM_VERSION}_linux_amd64.zip -d /usr/local/bin/

RUN \
  wget https://github.com/cloudfoundry/bosh-bootloader/releases/download/v${BBL_VERSION}/bbl-v${BBL_VERSION}_linux_x86-64 -P /tmp && \
  mv /tmp/bbl-* /usr/local/bin/bbl && \
  chmod +x /usr/local/bin/bbl

RUN \
  wget https://github.com/cloudfoundry-incubator/routing-api-cli/releases/download/${ROUTING_API_CLI_VERSION}/rtr-linux-amd64.tgz && \
  tar -xvf rtr-linux-amd64.tgz && \
  mv rtr-linux-amd64 /usr/local/bin/rtr

RUN \
  wget https://s3.amazonaws.com/bosh-cli-artifacts/bosh-cli-${BOSH_CLI_VERSION}-linux-amd64 && \
  chmod +x bosh-cli-${BOSH_CLI_VERSION}-linux-amd64 && \
  mv bosh-cli-${BOSH_CLI_VERSION}-linux-amd64 /usr/local/bin/bosh2 && \
  cp /usr/local/bin/bosh2 /usr/local/bin/bosh

# install certstrap
RUN go get \
  -u github.com/square/certstrap
