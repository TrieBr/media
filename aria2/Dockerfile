# Builds and runs Aria2 (https://github.com/aria2/aria2) server.
FROM ubuntu:jammy AS base
EXPOSE 6789
RUN apt-get -y update --allow-insecure-repositories
RUN apt-get install -y --allow-unauthenticated git libgnutls28-dev nettle-dev libgmp-dev libssh2-1-dev libc-ares-dev libxml2-dev zlib1g-dev libsqlite3-dev pkg-config libxml2-dev libcppunit-dev autoconf automake autotools-dev autopoint libtool clang make
RUN git clone https://github.com/aria2/aria2.git /app
WORKDIR /app
RUN autoreconf -i
RUN sh configure --disable-dependency-tracking
RUN make
ENTRYPOINT /app/src/aria2c --enable-rpc --disable-ipv6=true --rpc-listen-all
