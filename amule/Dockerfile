FROM ubuntu:20.04
LABEL MAINTAINER "branchial@protonmail.com"

ARG DEBIAN_FRONTEND=noninteractive

ENV TZ=Europe/Berlin

ENV AMULE_VERSION 2.3.2
ENV AMULE_BRANCH fork
# ENV UPNP_VERSION 1.14.0
# ENV CRYPTOPP_VERSION CRYPTOPP_8_4_0

RUN buildDeps='build-essential debhelper libgd-dev libgtk-3-dev zlib1g-dev libwxgtk3.0-gtk3-dev \
       bison flex libcrypto++-dev libgeoip-dev libupnp-dev git' \
 && apt-get update && apt-get -y upgrade && apt-get -y dist-upgrade \
 && apt-get -y install libcrypto++6 libgtk-3-0 libgd3 libgeoip1 libupnp13 zlib1g autopoint libcrypto++-utils libwxbase3.0-0v5 bash ca-certificates sudo \
 && update-ca-certificates \
 && apt-get -y install $buildDeps --no-install-recommends \
 #
 # compile amule 
 && mkdir -p /opt/amule \
 && git clone --branch ${AMULE_BRANCH} --single-branch "https://github.com/branchial/amule" /opt/amule \
 && cd /opt/amule \
 && ./autogen.sh \
 && ./configure \
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
        --disable-debug \
 && make \
 && make install \
 #
 # Install a nicer web ui
 && cd /usr/share/amule/webserver \
 && git clone https://github.com/MatteoRagni/AmuleWebUI-Reloaded \
 #
 # Clean up
 && rm -rf AmuleWebUI-Reloaded/.git AmuleWebUI-Reloaded/doc-images \
 && apt-get purge -y --auto-remove $buildDeps \
 && rm -rf \
	/tmp/* \
	/var/cache/apt/archives/* \
	/var/lib/apt/lists/* \
	/var/tmp/* \
       /var/cache/apk/* \
       /opt

# Add startup script
ADD amule.sh /home/amule/amule.sh
EXPOSE 4711/tcp 4712/tcp 4672/udp 4665/udp 4662/tcp 4661/tcp
ENTRYPOINT ["/home/amule/amule.sh"]