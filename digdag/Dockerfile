FROM docker:18.09-dind

# 参考
# https://github.com/myui/dockernized-digdag-server/blob/master/Dockerfile

MAINTAINER yohei.maeda@ghs.tokyo

# java
# https://github.com/docker-library/openjdk/blob/266f01a904151eab55302df4a306a5042e77f58b/12/jdk/alpine/Dockerfile
ENV JAVA_HOME /opt/openjdk-12
ENV PATH $JAVA_HOME/bin:$PATH

# http://jdk.java.net/
ENV JAVA_VERSION 12-ea+20
ENV JAVA_URL https://download.java.net/java/early_access/alpine/20/binaries/openjdk-12-ea+20_linux-x64-musl_bin.tar.gz
ENV JAVA_SHA256 125162b84369be592f8355624075f579795343a835706f0cbde065882d9404a1
# "For Alpine Linux, builds are produced on a reduced schedule and may not be in sync with the other platforms."

RUN set -eux; \
	\
	wget -O /openjdk.tgz "$JAVA_URL"; \
	echo "$JAVA_SHA256 */openjdk.tgz" | sha256sum -c -; \
	mkdir -p "$JAVA_HOME"; \
	tar --extract --file /openjdk.tgz --directory "$JAVA_HOME" --strip-components 1; \
	rm /openjdk.tgz; \
	\
# https://github.com/docker-library/openjdk/issues/212#issuecomment-420979840
# http://openjdk.java.net/jeps/341
	java -Xshare:dump; \
	\
# basic smoke test
	java --version; \
	javac --version


# digdag

ENV DIGDAG_VERSION=0.9.31

RUN apk add --no-cache curl && \
    curl -o /usr/bin/digdag --create-dirs -L "https://dl.digdag.io/digdag-$DIGDAG_VERSION" && \
    chmod +x /usr/bin/digdag && \
    apk del curl && \
    adduser -h /var/lib/digdag -g 'digdag user' -s /sbin/nologin -D digdag && \
    mkdir -p /var/lib/digdag/logs/tasks /var/lib/digdag/logs/server && \
    chown -R digdag.digdag /var/lib/digdag && \
    \
    apk --no-cache update && \
    apk --no-cache add python py-pip py-setuptools ca-certificates curl groff less && \
    apk --no-cache add bash jq && \
    apk --no-cache add openrc && \
    pip --no-cache-dir install awscli && \
    rm -rf /var/cache/apk/*

COPY digdag.properties /etc/digdag.properties

USER root

WORKDIR /var/lib/digdag


EXPOSE 65432
CMD exec digdag server --bind 0.0.0.0 \
                       --port 65432 \
                       --config /etc/digdag.properties \
		               --log /var/log/digdag/logs/server \
		               --task-log /var/log/digdag/logs/tasks \
                       -X database.type=$DB_TYPE \
                       -X database.user=$DB_USER \
                       -X database.password=$DB_PASSWORD \
                       -X database.host=$DB_HOST \
                       -X database.port=$DB_PORT \
                       -X database.database=$DB_NAME


