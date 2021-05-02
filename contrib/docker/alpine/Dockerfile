# vim:set ft=dockerfile:
# This is a Dockerfile for firod.
FROM alpine:3 AS firo

MAINTAINER Alexey Zilber <azilber@consultent.ltd>

# Install required system packages
RUN apk update
RUN apk --no-cache add automake
RUN apk --no-cache add autoconf
RUN apk --no-cache add build-base
RUN apk --no-cache add cmake
RUN apk --no-cache add curl
RUN apk --no-cache add g++
RUN apk --no-cache add boost-build
RUN apk --no-cache add boost-dev
RUN apk --no-cache add boost-program_options
RUN apk --no-cache add libevent-dev
RUN apk --no-cache add openssl-dev
RUN apk --no-cache add libtool
RUN apk --no-cache add zeromq-dev
RUN apk --no-cache add libzmq
RUN apk --no-cache add gmp-dev
RUN apk --no-cache add make
RUN apk --no-cache add pkgconf
RUN apk --no-cache add zlib-dev
RUN apk --no-cache add git
RUN apk --no-cache add openjdk9-jdk
RUN apk --no-cache add minizip
RUN apk --no-cache add minizip-dev


# Install Berkeley DB 4.8.30
# atomic.h fix from https://github.com/uphold/docker-dash-core/blob/master/0.12/alpine/Dockerfile
RUN curl -L http://download.oracle.com/berkeley-db/db-4.8.30.tar.gz | tar -xz -C /tmp && \
    cd /tmp/db-4.8.30/build_unix && \
    sed s/__atomic_compare_exchange/__atomic_compare_exchange_db/g -i ../dbinc/atomic.h && \
    ../dist/configure --enable-cxx --includedir=/usr/include/bdb4.8 --libdir=/usr/lib && \
    make -j4 && make install && \
    cd / && rm -rf /tmp/db-4.8.30


# Create user to run daemon
RUN adduser -D firod

# Get Firo
RUN cd /tmp && git clone https://github.com/firoorg/firo.git

# Build Firo
RUN cd /tmp/firo && \
    ./autogen.sh && \
    ./configure --without-gui --prefix=/usr && \
    make -j$(nproc) && \
    make check && \
    make install && \
    cd / && rm -rf /tmp/firo

# Remove unused packages
RUN apk del boost-dev libevent-dev openssl-dev gmp-dev zlib-dev minizip-dev

# Start Firo Daemon
USER firod

RUN mkdir /home/firod/.firo
VOLUME [ "/home/firod/.firo" ]

# Main network ports
EXPOSE 8168
EXPOSE 8888

# Test network ports
EXPOSE 18168
EXPOSE 18888

# Regression test network ports
EXPOSE 18444
EXPOSE 28888

ENTRYPOINT [ "/usr/bin/firod" ]
