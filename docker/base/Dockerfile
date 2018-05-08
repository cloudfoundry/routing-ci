FROM ubuntu:trusty
MAINTAINER https://github.com/cloudfoundry-incubator/cf-routing-release

RUN \
      apt-get update && \
      apt-get -y install --fix-missing \
            build-essential \
            curl \
            git \
            graphviz \
            htop \
            iptables \
            jq \
            libfontconfig1-dev libfreetype6 libssl-dev libpng-dev libjpeg-dev \
            libicu52 \
            libmysqlclient-dev \
            libpython-dev \
            libreadline6 \
            libreadline6-dev\
            libssl-dev \
            libssl-dev/trusty \
            libssl1.0.0/trusty \
            libxml2-dev \
            libxslt-dev \
            libyaml-dev \
            lsof \
            mercurial \
            mysql-server \
            openssl/trusty \
            psmisc \
            python \
            python-pip \
            strace \
            unzip \
            wget \
            zip \
            zlib1g-dev \
      && \
      apt-get clean

# install sshuttle
RUN pip install sshuttle

