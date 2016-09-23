FROM ruby:2.2.3-slim

ENV PHANTOMJS_VERSION 2.1.1

ENV GOCD_VERSION=16.7.0 \
  GOCD_RELEASE=go-agent \
  GOCD_REVISION=3819 \
  GOCD_HOME=/opt/go-agent \
  PATH=$GOCD_HOME:$PATH

ENV GOCD_REPO=https://download.go.cd/binaries/${GOCD_VERSION}-${GOCD_REVISION}/generic \
  GOCD_RELEASE_ARCHIVE=${GOCD_RELEASE}-${GOCD_VERSION}-${GOCD_REVISION}.zip \
  SERVER_WORK_DIR=${GOCD_HOME}/work

# Install phantomjs
RUN cd /tmp && \
    curl -L -O https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-$PHANTOMJS_VERSION-linux-x86_64.tar.bz2 && \
    tar xjf /tmp/phantomjs-$PHANTOMJS_VERSION-linux-x86_64.tar.bz2 -C /tmp && \
    mv /tmp/phantomjs-$PHANTOMJS_VERSION-linux-x86_64/bin/phantomjs /usr/local/bin && \
    rm -rf /tmp/phantomjs-$PHANTOMJS_VERSION-linux-x86_64

RUN apt-get update && \
    apt-get install -y \
      zip \
      build-essential \
      libmysqlclient-dev \
      mysql-client \
      libfreetype6 \
      libfontconfig1 &&\
    mkdir /var/log/go-agent /var/run/go-agent &&\
    cd /opt && curl -sSL ${GOCD_REPO}/${GOCD_RELEASE_ARCHIVE} -O && unzip ${GOCD_RELEASE_ARCHIVE} && rm ${GOCD_RELEASE_ARCHIVE} &&\
    mv /opt/${GOCD_RELEASE}-${GOCD_VERSION} ${GOCD_HOME} &&\
    chmod 774 ${GOCD_HOME}/*.sh &&\
    mkdir -p ${GOCD_HOME}/work &&\
    apt-get clean &&\
    rm -rf /var/lib/apt/lists &&\
    gem install bundler

COPY docker-entrypoint.sh /usr/bin/docker-entrypoint.sh
RUN chmod +x /usr/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/bin/docker-entrypoint.sh"]