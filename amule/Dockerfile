FROM ubuntu:20.04
LABEL MAINTAINER "branchial@protonmail.com"

ENV TZ=Europe/Berlin
ENV DEBIAN_FRONTEND=noninteractive

# install build environment
RUN apt-get update && apt-get -y upgrade && apt-get -y dist-upgrade 
RUN apt-get -y install libcrypto++6 libgtk-3-0 libgd3 libgeoip1 libupnp13 zlib1g autopoint libcrypto++-utils libwxbase3.0-0v5 bash ca-certificates sudo git
RUN update-ca-certificates

# add appuser account
RUN groupadd --gid 5000 appgroup
RUN useradd -m -u 5000 -g appgroup -s /bin/bash -d /home/appuser appuser
RUN sed -i /etc/sudoers -re 's/^%sudo.*/%sudo ALL=(ALL:ALL) NOPASSWD: ALL/g' &&\
    echo "appuser ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
# work in user context
USER appuser

# install build environment
RUN buildDeps='build-essential debhelper libgd-dev libgtk-3-dev zlib1g-dev libwxgtk3.0-gtk3-dev \
               bison flex libcrypto++-dev libgeoip-dev libupnp-dev' &&\
    sudo apt-get -y install $buildDeps --no-install-recommends &&\
    cd /home/appuser && \
    git clone --branch master --single-branch "https://github.com/branchial/amule" src && \
    cd src &&\
    ./autogen.sh &&\
    ./configure \
        --disable-gui \
        --disable-amule-gui \
        --disable-wxcas \
        --disable-alc \
        --disable-plasmamule \
        --disable-kde-in-home \
        --prefix=/usr \
        --mandir=/usr/share/man \
        --enable-unicode \
        --without-subdirs \
        --without-expat \
        --enable-amule-daemon \
        --enable-amulecmd \
        --enable-webserver \
        --enable-cas \
        --enable-alcc \
        --enable-fileview \
        --enable-geoip \
        --enable-mmap \
        --enable-optimize \
        --enable-upnp \
        --disable-debug &&\
    make &&\
    sudo make install &&\
    cd .. && \
    rm -r src &&\
    sudo apt-get clean && \
    sudo apt-get purge -y --auto-remove $buildDeps &&\
# Install a nicer web ui
    cd /usr/share/amule/webserver &&\
    sudo git clone https://github.com/MatteoRagni/AmuleWebUI-Reloaded &&\
#
# Clean up
    sudo apt purge -y --auto-remove git && \
    sudo rm -rf AmuleWebUI-Reloaded/.git AmuleWebUI-Reloaded/doc-images &&\
    sudo rm -rf \
	   /tmp/* \
	   /var/cache/apt/archives/* \
 	   /var/lib/apt/lists/* \
 	   /var/tmp/* \
       /var/cache/apk/*

# Add startup script
ADD amule.sh ~/amule.sh
EXPOSE 4711/tcp 4712/tcp 4672/udp 4665/udp 4662/tcp 4661/tcp
ENTRYPOINT ["~/amule.sh"]
