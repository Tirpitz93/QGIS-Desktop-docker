FROM golang:1.14-buster AS easy-novnc-build

WORKDIR /src
RUN --mount=type=cache,target=/root/.cache/go-build \
    go mod init build  &&\
    go get github.com/geek1011/easy-novnc@v1.1.0 && \
    go build -o /bin/easy-novnc github.com/geek1011/easy-novnc

FROM debian:buster

RUN --mount=target=/var/lib/apt/lists,type=cache,sharing=locked \
    --mount=target=/var/cache/apt,type=cache,sharing=locked \
    rm -f /etc/apt/apt.conf.d/docker-clean &&\
    apt-get update -y && \
    apt-get install -y --no-install-recommends  gnupg software-properties-common \
    openbox tigervnc-standalone-server supervisor gosu  gnupg \
    software-properties-common lxterminal nano wget openssh-client rsync \
    ca-certificates xdg-utils htop tar xzip gzip bzip2 zip unzip


RUN mkdir -m755 -p /etc/apt/keyrings

RUN echo "Types: deb deb-src" > /etc/apt/sources.list.d/qgis.sources
RUN echo "URIs: https://qgis.org/debian" >> /etc/apt/sources.list.d/qgis.sources
RUN echo "Suites: `env -i bash -c '. /etc/os-release; echo $VERSION_CODENAME'`" >> /etc/apt/sources.list.d/qgis.sources
RUN echo "Architectures: amd64" >> /etc/apt/sources.list.d/qgis.sources
RUN echo "Components: main" >> /etc/apt/sources.list.d/qgis.sources
RUN echo "Signed-By: /etc/apt/keyrings/qgis-archive-keyring.gpg" >> /etc/apt/sources.list.d/qgis.sources

run echo "TEST"
RUN cat /etc/apt/sources.list.d/qgis.sources
#
RUN wget -O /etc/apt/keyrings/qgis-archive-keyring.gpg https://download.qgis.org/downloads/qgis-archive-keyring.gpg
#
RUN --mount=target=/var/lib/apt/lists,type=cache,sharing=locked \
    --mount=target=/var/cache/apt,type=cache,sharing=locked \
    rm -f /etc/apt/apt.conf.d/docker-clean &&\
    apt update &&  apt-get install qgis qgis-plugin-grass python-qgis qgis-providers  qgis-common -y

COPY --from=easy-novnc-build /bin/easy-novnc /usr/local/bin/
COPY menu.xml /etc/xdg/openbox/
COPY supervisord.conf /etc/


RUN groupadd --gid 1000 app && \
    useradd --home-dir /data --shell /bin/bash --uid 1000 --gid 1000 app && \
    mkdir -p /data
VOLUME /data

CMD ["sh", "-c", "chown app:app /data /dev/stdout && exec gosu app supervisord"]
