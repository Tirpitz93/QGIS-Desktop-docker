FROM golang:bookworm AS easy-novnc-build
WORKDIR /src
RUN go mod init build && \
    go get github.com/geek1011/easy-novnc@v1.1.0 && \
    go build -o /bin/easy-novnc github.com/geek1011/easy-novnc


FROM debian:bookworm

RUN apt-get update -y && \
    apt-get install -y --no-install-recommends openbox tigervnc-standalone-server supervisor gosu gnupg software-properties-common wget && \
    rm -rf /var/lib/apt/lists

RUN apt-get update -y && \
        apt-get install gnupg software-properties-common wget -y \

RUN  mkdir -p /usr/share/desktop-directories
RUN apt-get update -y && \
    apt-get install -y --no-install-recommends \
    lxterminal \
    nano \
    wget \
    openssh-client \
    rsync \
    ca-certificates \
    xdg-utils \
    htop \
    tar \
    xzip \
    gzip \
    bzip2 \
    zip \
    unzip && \
    rm -rf /var/lib/apt/lists

ENV PATH "$PATH:/usr/lib/ccache"
RUN mkdir -p ${HOME}/dev/cpp && \
    cd ${HOME}/dev/cpp



COPY qgis.sources /etc/apt/sources.list.d/qgis.sources

RUN wget -O /etc/apt/keyrings/qgis-archive-keyring.gpg https://download.qgis.org/downloads/qgis-archive-keyring.gpg
RUN sed -i "s/{codename}/`lsb_release -cs`/g" /etc/apt/sources.list.d/qgis.sources

RUN apt-get update

RUN apt-get install qgis qgis-plugin-grass -y
#RUN exit 1
COPY --from=easy-novnc-build /bin/easy-novnc /usr/local/bin/
COPY menu.xml /etc/xdg/openbox/
COPY supervisord.conf /etc/


RUN groupadd --gid 1000 app && \
    useradd --home-dir /data --shell /bin/bash --uid 1000 --gid 1000 app && \
    mkdir -p /data
VOLUME /data

CMD ["sh", "-c", "chown app:app /data /dev/stdout && exec gosu app supervisord"]
