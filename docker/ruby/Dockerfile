FROM ubuntu:trusty
MAINTAINER https://github.com/cloudfoundry-incubator/cf-routing-release

ENV DOCKER_RUBY_VERSION 2.2.3
ENV RUBY_INSTALLER_VERSION 0.5.0

# Install ruby-install
RUN curl https://codeload.github.com/postmodern/ruby-install/tar.gz/v${RUBY_INSTALLER_VERSION} | tar xvz -C /tmp/ && \
  cd /tmp/ruby-install-${RUBY_INSTALLER_VERSION} && \
  make install

# Install Ruby
RUN ruby-install ruby ${DOCKER_RUBY_VERSION} -- --disable-install-rdoc

# Add ruby to PATH
ENV PATH $PATH:/home/root/.gem/ruby/${DOCKER_RUBY_VERSION}/bin:/opt/rubies/ruby-${DOCKER_RUBY_VERSION}/lib/ruby/gems/${DOCKER_RUBY_VERSION}/bin:/opt/rubies/ruby-${DOCKER_RUBY_VERSION}/bin

# Install gems
RUN  /opt/rubies/ruby-${DOCKER_RUBY_VERSION}/bin/gem install --no-rdoc --no-ri \
  bundler \
  debugger-ruby_core_source

