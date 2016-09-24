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
    apt-get install -y --no-install-recommends \
      zip bzip2 unzip xz-utils git-all openssh-client \
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

RUN echo 'deb http://httpredir.debian.org/debian jessie-backports main' > /etc/apt/sources.list.d/jessie-backports.list

ENV LANG C.UTF-8

RUN { \
		echo '#!/bin/sh'; \
		echo 'set -e'; \
		echo; \
		echo 'dirname "$(dirname "$(readlink -f "$(which javac || which java)")")"'; \
	} > /usr/local/bin/docker-java-home \
	&& chmod +x /usr/local/bin/docker-java-home

ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/jre

ENV JAVA_VERSION 8u102
ENV JAVA_DEBIAN_VERSION 8u102-b14.1-1~bpo8+1

ENV CA_CERTIFICATES_JAVA_VERSION 20140324

RUN set -x \
	&& apt-get update \
	&& apt-get install -y \
		openjdk-8-jre-headless="$JAVA_DEBIAN_VERSION" \
		ca-certificates-java="$CA_CERTIFICATES_JAVA_VERSION" \
	&& rm -rf /var/lib/apt/lists/* \
	&& [ "$JAVA_HOME" = "$(docker-java-home)" ]

RUN /var/lib/dpkg/info/ca-certificates-java.postinst configure

COPY docker-entrypoint.sh /usr/bin/docker-entrypoint.sh
RUN chmod +x /usr/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/bin/docker-entrypoint.sh"]