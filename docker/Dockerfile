FROM ubuntu:trusty
MAINTAINER https://github.com/cloudfoundry-incubator/cf-routing-release

RUN set -ex \
    && echo 'mysql-server mysql-server/root_password password password' | debconf-set-selections \
    && echo 'mysql-server mysql-server/root_password_again password password' | debconf-set-selections

RUN \
      apt-get update && \
      apt-get -y install --fix-missing \
            mysql-server \
            build-essential \
            curl \
            git \
            htop \
            graphviz \
            libpython-dev \
            lsof \
            psmisc \
            python \
            strace \
            libreadline6 \
            libreadline6-dev\
            wget \
            unzip \
            libmysqlclient-dev \
            libicu52 \
            libfontconfig1-dev libfreetype6 libssl-dev libpng-dev libjpeg-dev \
            jq \
            libssl-dev \
            libssl-dev/trusty \
            libssl1.0.0/trusty \
            libxml2-dev \
            libxslt-dev \
            libyaml-dev \
            openssl/trusty \
            zip \
            python-pip \
            zlib1g-dev \
      && \
      apt-get clean

# Install ruby-install
RUN curl https://codeload.github.com/postmodern/ruby-install/tar.gz/v0.5.0 | tar xvz -C /tmp/ && \
          cd /tmp/ruby-install-0.5.0 && \
          make install

ENV DOCKER_RUBY_VERSION 2.2.3

# Install Ruby
RUN ruby-install ruby ${DOCKER_RUBY_VERSION} -- --disable-install-rdoc

# Add ruby to PATH
ENV PATH $PATH:/home/root/.gem/ruby/${DOCKER_RUBY_VERSION}/bin:/opt/rubies/ruby-2.2.3/lib/ruby/gems/${DOCKER_RUBY_VERSION}/bin:/opt/rubies/ruby-${DOCKER_RUBY_VERSION}/bin

# Install gems
RUN \
  /opt/rubies/ruby-${DOCKER_RUBY_VERSION}/bin/gem install --no-rdoc --no-ri \
    bundler \
    debugger-ruby_core_source

# Install go
ENV GO_VERSION 1.9.1
RUN curl https://storage.googleapis.com/golang/go${GO_VERSION}.linux-amd64.tar.gz | tar xvz -C /usr/local/

# Add golang environment variables
ENV HOME /root
ENV GOPATH /root/go
ENV PATH $PATH:/usr/local/go/bin:/root/bin:$GOPATH/bin

# install Mercurial (for hg go dependencies)
RUN apt-get update && apt-get -y install --no-install-recommends mercurial && apt-get clean

# install common CI dependencies
RUN \
  go get \
    golang.org/x/tools/cmd/cover

# Install spiff
RUN wget -O /tmp/spiff.zip https://github.com/cloudfoundry-incubator/spiff/releases/download/v1.0.7/spiff_linux_amd64.zip
RUN unzip /tmp/spiff.zip -d /tmp/ && mv /tmp/spiff $GOPATH/bin/

# Install aws cli
RUN wget https://bootstrap.pypa.io/get-pip.py -O /tmp/get-pip.py && \
          python /tmp/get-pip.py && \
          rm /tmp/get-pip.py && \
          pip install awscli

RUN \
  wget https://releases.hashicorp.com/vagrant/1.7.4/vagrant_1.7.4_x86_64.deb -P /tmp && \
  dpkg --install /tmp/vagrant_1.7.4_x86_64.deb && \
  vagrant plugin install vagrant-aws && \
  rm -rf /tmp/*

RUN \
  curl -sL https://cli.run.pivotal.io/stable\?release\=linux64-binary\&version\=6.20.0\&source\=github-rel | tar zxf - -C /usr/bin


# Add the PostgreSQL PGP key to verify their Debian packages.
# # It should be the same key as https://www.postgresql.org/media/keys/ACCC4CF8.asc
RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8
# Add the PostgreSQL repo
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" > /etc/apt/sources.list.d/pgdg.list

# Install postgres
RUN \
  apt-get update && \
  apt-get -y install --fix-missing \
    python-software-properties \
    software-properties-common \
    postgresql-9.3 \
    postgresql-client-9.3 \
    postgresql-contrib-9.3 \
  && \
  apt-get clean

RUN sed -i 's/peer/trust/' "$(find /etc/postgresql -name pg_hba.conf)" \
    && sed -i 's/md5/trust/' "$(find /etc/postgresql -name pg_hba.conf)"

# Correct the Error: could not open temporary statistics file "/var/run/postgresql/9.3-main.pg_stat_tmp/global.tmp": No such file or directory
RUN mkdir -p /var/run/postgresql/9.3-main.pg_stat_tmp
RUN chown postgres.postgres /var/run/postgresql/9.3-main.pg_stat_tmp -R

# bbl (not all dependencies have been included because we only are using certain bbl commands)
ENV BBL_VERSION 5.9.0
RUN \
  wget https://github.com/cloudfoundry/bosh-bootloader/releases/download/v${BBL_VERSION}/bbl-v${BBL_VERSION}_linux_x86-64 -P /tmp && \
  mv /tmp/bbl-* /usr/local/bin/bbl && \
  chmod +x /usr/local/bin/bbl

RUN \
  wget https://github.com/cloudfoundry-incubator/routing-api-cli/releases/download/2.9.0/rtr-linux-amd64.tgz && \
  tar -xvf rtr-linux-amd64.tgz && \
  mv rtr-linux-amd64 /usr/local/bin/rtr

RUN \
  wget https://github.com/coreos/etcd/releases/download/v2.1.1/etcd-v2.1.1-linux-amd64.tar.gz && \
  tar xvf etcd-v2.1.1-linux-amd64.tar.gz -C /tmp && \
  mv /tmp/etcd-v2.1.1-linux-amd64/etcd /usr/bin/etcd

RUN \
  wget https://releases.hashicorp.com/consul/0.7.5/consul_0.7.5_linux_amd64.zip && \
  unzip consul_0.7.5_linux_amd64.zip -d /usr/bin

# Install bosh v2 cli
RUN \
  wget https://s3.amazonaws.com/bosh-cli-artifacts/bosh-cli-2.0.45-linux-amd64 && \
  chmod +x bosh-cli-2.0.45-linux-amd64 && \
  mv bosh-cli-2.0.45-linux-amd64 /usr/local/bin/bosh2 && \
  cp /usr/local/bin/bosh2 /usr/local/bin/bosh

# setup mysql database for testing, then stop and clean up
RUN set -ex \
    && mkdir -p /var/lib/mysql/tmp /var/log/mysql /var/run/mysqld \
    && chown -R mysql:mysql /var/lib/mysql /var/log/mysql /var/run/mysqld \
    && chmod -R 755 /var/lib/mysql \
    && sed -i 's%/tmp%/var/lib/mysql/tmp%' /etc/mysql/my.cnf \
    && service mysql restart \
    && echo 'CREATE DATABASE routing_api_test;' | mysql -u root -ppassword \
    && rm -rf /tmp/* /var/tmp/* /var/run/mysqld/* /var/lib/mysql/tmp/*

# install sshuttle and iptables
RUN \
  apt-get update && \
  apt-get -y install iptables && \
  apt-get clean && \
  pip install sshuttle

# install glide
RUN \
  add-apt-repository ppa:masterminds/glide && \
  apt-get update && \
  apt-get -y install glide && \
  apt-get clean

# install go dep
RUN \
    go get \
        -u github.com/golang/dep/cmd/dep
