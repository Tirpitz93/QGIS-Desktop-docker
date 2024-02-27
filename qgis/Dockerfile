FROM golang:bookworm AS easy-novnc-build
WORKDIR /src
RUN go mod init build && \
    go get github.com/geek1011/easy-novnc@v1.1.0 && \
    go build -o /bin/easy-novnc github.com/geek1011/easy-novnc


FROM debian:bookworm

RUN apt-get update -y && \
    apt-get install -y --no-install-recommends openbox tigervnc-standalone-server supervisor gosu install gnupg software-properties-common wget && \
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
    unzip \
    apt-get \
    install \
    bison \
    build-essential \
    ca-certificates \
    ccache \
    cmake \
    cmake-curses-gui \
    dh-python \
    doxygen \
    expect \
    flex \
    flip \
    gdal-bin \
    git \
    graphviz \
    grass-dev \
    libdraco-dev \
    libexiv2-dev \
    libexpat1-dev \
    libfcgi-dev \
    libgdal-dev \
    libgeos-dev \
    libgsl-dev \
    libpq-dev \
    libproj-dev \
    libprotobuf-dev \
    libqca-qt5-2-dev \
    libqca-qt5-2-plugins \
    libqscintilla2-qt5-dev \
    libqt5opengl5-dev \
    libqt5serialport5-dev \
    libqt5sql5-sqlite \
    libqt5svg5-dev \
    libqt5webkit5-dev \
    libqt5xmlpatterns5-dev \
    libqwt-qt5-dev \
    libspatialindex-dev \
    libspatialite-dev \
    libsqlite3-dev \
    libsqlite3-mod-spatialite \
    libyaml-tiny-perl \
    libzip-dev \
    libzstd-dev \
    lighttpd \
    locales \
    ninja-build \
    ocl-icd-opencl-dev \
    opencl-headers \
    pandoc \
    pkg-config \
    poppler-utils \
    protobuf-compiler \
    pyqt5-dev \
    pyqt5-dev-tools \
    pyqt5.qsci-dev \
    python3-all-dev \
    python3-autopep8 \
    python3-dev \
    python3-gdal \
    python3-jinja2 \
    python3-lxml \
    python3-mock \
    python3-nose2 \
    python3-owslib \
    python3-plotly \
    python3-psycopg2 \
    python3-pygments \
    python3-pyproj \
    python3-pyqt5 \
    python3-pyqt5.qsci \
    python3-pyqt5.qtmultimedia \
    python3-pyqt5.qtpositioning \
    python3-pyqt5.qtserialport \
    python3-pyqt5.qtsql \
    python3-pyqt5.qtsvg \
    python3-pyqt5.qtwebkit \
    python3-pyqtbuild \
    python3-sip \
    python3-termcolor \
    python3-yaml \
    qt3d-assimpsceneimport-plugin \
    qt3d-defaultgeometryloader-plugin \
    qt3d-gltfsceneio-plugin \
    qt3d-scene2d-plugin \
    qt3d5-dev \
    qtbase5-dev \
    qtbase5-private-dev \
    qtkeychain-qt5-dev \
    qtmultimedia5-dev \
    qtpositioning5-dev \
    qttools5-dev \
    qttools5-dev-tools \
    sip-tools \
    spawn-fcgi \
    xauth \
    xfonts-100dpi \
    xfonts-75dpi \
    xfonts-base \
    xfonts-scalable \
    xvfb && \
    rm -rf /var/lib/apt/lists

ENV PATH "$PATH:/usr/lib/ccache"
RUN mkdir -p ${HOME}/dev/cpp && \
    cd ${HOME}/dev/cpp



COPY qgis.sources /etc/apt/sources.list.d/qgis.sources

RUN wget -O /etc/apt/keyrings/qgis-archive-keyring.gpg https://download.qgis.org/downloads/qgis-archive-keyring.gpg
RUN sed -i "s/{codename}/`lsb_release -cs`/g" /etc/apt/sources.list.d/qgis.sources

RUN apt update &&  apt list qgis -a

RUN apt-src install qgis qgis-plugin-grass -y
#RUN exit 1
COPY --from=easy-novnc-build /bin/easy-novnc /usr/local/bin/
COPY menu.xml /etc/xdg/openbox/
COPY supervisord.conf /etc/


RUN groupadd --gid 1000 app && \
    useradd --home-dir /data --shell /bin/bash --uid 1000 --gid 1000 app && \
    mkdir -p /data
VOLUME /data

CMD ["sh", "-c", "chown app:app /data /dev/stdout && exec gosu app supervisord"]
