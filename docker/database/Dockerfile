FROM ubuntu:trusty
MAINTAINER https://github.com/cloudfoundry-incubator/cf-routing-release

ENV POSTGRES_VERSION 9.3
ENV ETCD_VERSION 2.1.1
ENV CONSUL_VERSION 0.7.5

RUN set -ex \
    && echo 'mysql-server mysql-server/root_password password password' | debconf-set-selections \
    && echo 'mysql-server mysql-server/root_password_again password password' | debconf-set-selections

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
    postgresql-${POSTGRES_VERSION} \
    postgresql-client-${POSTGRES_VERSION} \
    postgresql-contrib-${POSTGRES_VERSION} \
  && \
  apt-get clean

RUN sed -i 's/peer/trust/' "$(find /etc/postgresql -name pg_hba.conf)" \
    && sed -i 's/md5/trust/' "$(find /etc/postgresql -name pg_hba.conf)"

# Correct the Error: could not open temporary statistics file "/var/run/postgresql/9.3-main.pg_stat_tmp/global.tmp": No such file or directory
RUN mkdir -p /var/run/postgresql/${POSTGRES_VERSION}-main.pg_stat_tmp

RUN chown postgres.postgres /var/run/postgresql/${POSTGRES_VERSION}-main.pg_stat_tmp -R

RUN \
  wget https://github.com/coreos/etcd/releases/download/v${ETCD_VERSION}/etcd-v${ETCD_VERSION}-linux-amd64.tar.gz && \
  tar xvf etcd-v${ETCD_VERSION}-linux-amd64.tar.gz -C /tmp && \
  mv /tmp/etcd-v${ETCD_VERSION}-linux-amd64/etcd /usr/bin/etcd

RUN \
  wget https://releases.hashicorp.com/consul/${CONSUL_VERSION}/consul_${CONSUL_VERSION}_linux_amd64.zip && \
  unzip consul_${CONSUL_VERSION}_linux_amd64.zip -d /usr/bin

# setup mysql database for testing, then stop and clean up
RUN set -ex \
    && mkdir -p /var/lib/mysql/tmp /var/log/mysql /var/run/mysqld \
    && chown -R mysql:mysql /var/lib/mysql /var/log/mysql /var/run/mysqld \
    && chmod -R 755 /var/lib/mysql \
    && sed -i 's%/tmp%/var/lib/mysql/tmp%' /etc/mysql/my.cnf \
    && service mysql restart \
    && echo 'CREATE DATABASE routing_api_test;' | mysql -u root -ppassword \
    && rm -rf /tmp/* /var/tmp/* /var/run/mysqld/* /var/lib/mysql/tmp/*

