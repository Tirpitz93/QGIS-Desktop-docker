FROM golang:1.14-buster AS easy-novnc-build
LABEL authors="lselter"

WORKDIR /src
RUN --mount=type=cache,target=/root/.cache/go-build \
    go mod init build  &&\
    go get github.com/geek1011/easy-novnc@v1.1.0 && \
    go build -o /bin/easy-novnc github.com/geek1011/easy-novnc

FROM debian:buster

RUN apt-get update -y && \
    apt-get install -y --no-install-recommends  gnupg software-properties-common \
    openbox tigervnc-standalone-server supervisor gosu  gnupg \
    software-properties-common lxterminal nano wget openssh-client rsync \
    ca-certificates xdg-utils htop tar xzip gzip bzip2 zip unzip


RUN apt update &&  apt-get install qgis qgis-plugin-grass -y

COPY --from=easy-novnc-build /bin/easy-novnc /usr/local/bin/
COPY menu.xml /etc/xdg/openbox/
COPY supervisord.conf /etc/


RUN groupadd --gid 1000 app && \
    useradd --home-dir /data --shell /bin/bash --uid 1000 --gid 1000 app && \
    mkdir -p /data
VOLUME /data

CMD ["sh", "-c", "chown app:app /data /dev/stdout && exec gosu app supervisord"]
