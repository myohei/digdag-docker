FROM docker:20.10.8-dind-alpine3.14

# 参考
# https://github.com/myui/dockernized-digdag-server/blob/master/Dockerfile

MAINTAINER yohei.maeda.11@gmail.com

# java
# https://github.com/docker-library/openjdk/blob/266f01a904/8/jdk/alpine/Dockerfile
# Default to UTF-8 file.encoding
ENV LANG C.UTF-8

# add a simple script that can auto-detect the appropriate JAVA_HOME value
# based on whether the JDK or only the JRE is installed
RUN { \
		echo '#!/bin/sh'; \
		echo 'set -e'; \
		echo; \
		echo 'dirname "$(dirname "$(readlink -f "$(which javac || which java)")")"'; \
	} > /usr/local/bin/docker-java-home \
	&& chmod +x /usr/local/bin/docker-java-home
ENV JAVA_HOME /usr/lib/jvm/java-1.8-openjdk
ENV PATH $PATH:/usr/lib/jvm/java-1.8-openjdk/jre/bin:/usr/lib/jvm/java-1.8-openjdk/bin

ENV JAVA_VERSION 8u282
ENV JAVA_ALPINE_VERSION 8.282.08-r1

RUN set -x \
    && apk update \
	&& apk add --no-cache \
		openjdk8="$JAVA_ALPINE_VERSION" \
	&& [ "$JAVA_HOME" = "$(docker-java-home)" ]


# digdag

ENV DIGDAG_VERSION=0.10.3

RUN apk add --no-cache curl && \
    curl -o /usr/bin/digdag --create-dirs -L "https://dl.digdag.io/digdag-$DIGDAG_VERSION" && \
    chmod +x /usr/bin/digdag && \
    apk del curl && \
    adduser -h /var/lib/digdag -g 'digdag user' -s /sbin/nologin -D digdag && \
    mkdir -p /var/lib/digdag/logs/tasks /var/lib/digdag/logs/server && \
    chown -R digdag.digdag /var/lib/digdag && \
    \
    apk --no-cache update && \
    apk --no-cache add ca-certificates curl groff less && \
    apk --no-cache add bash jq && \
    apk --no-cache add openrc && \
    rm -rf /var/cache/apk/*

COPY digdag.properties /etc/digdag.properties

USER root

WORKDIR /var/lib/digdag


EXPOSE $DIGDAG_PORT
CMD exec digdag server --bind 0.0.0.0 \
                       --port $DIGDAG_PORT \
                       --config /etc/digdag.properties \
		               --log /var/log/digdag/logs/server \
		               --task-log /var/log/digdag/logs/tasks \
                       -X database.type=$DB_TYPE \
                       -X database.user=$DB_USER \
                       -X database.password=$DB_PASSWORD \
                       -X database.host=$DB_HOST \
                       -X database.port=$DB_PORT \
                       -X database.database=$DB_NAME


