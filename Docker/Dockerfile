FROM debian:latest
MAINTAINER Antony Raj "antony.java@gmail.com"

# Update the packages
RUN apt-get update

# Install the pre-requisites for Rails
RUN apt-get install -y openssh-server git-core openssh-client curl
RUN apt-get install -y vim
RUN apt-get install -y build-essential python-software-properties
RUN apt-get install -y openssl libreadline6 libreadline6-dev curl zlib1g zlib1g-dev \
          libssl-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf \
          libc6-dev ncurses-dev automake libtool bison subversion pkg-config

# Install NodeJs
ENV NPM_CONFIG_LOGLEVEL info
ENV NODE_VERSION 7.7.2

RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
  && tar -xJf "node-v$NODE_VERSION-linux-x64.tar.xz" -C /usr/local --strip-components=1 \
  && rm "node-v$NODE_VERSION-linux-x64.tar.xz" \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs

#Install Yarn package manager for node
RUN /bin/bash -l -c "npm install --global yarn"
RUN /bin/bash -l -c "ln -s /usr/bin/nodejs /usr/bin/node"

#Install RVM
RUN \curl -L https://get.rvm.io | bash -s stable
RUN /bin/bash -l -c "rvm requirements"

# Install Ruby 2.4.0
RUN /bin/bash -l -c "rvm install 2.4.0"
RUN /bin/bash -l -c "gem install bundler --no-ri --no-rdoc"
RUN /bin/bash -l -c "echo gem: --no-document >> ~/.gemrc"

# This is done to prevent docker install gems all the time
COPY Gemfile* /app/

# Rails specific environment variables
ENV "RAILS_ENV" "production"
ENV SECRET_KEY_BASE "eee445de41b1590cc22eeb9b7e09bddb97fa37cd0b6b1936876e9b3c32094175174618f1cb8ea32aa8b1847a80b7b1888055deb4052ea0ec3db3"
ENV RAILS_SERVE_STATIC_FILES TRUE

# To set the work directory
WORKDIR /app
RUN /bin/bash -l -c "bundle install --without development test"
RUN /bin/bash -l -c "which bundle"

# To copy over application onto /app
ADD . /app

# Server is going to run on port 9292
EXPOSE 9292

# For running the application
CMD /bin/bash -l -c "rake assets:precompile && yarn install && ./bin/webpack && rails server -b 0.0.0.0 -p 9292"