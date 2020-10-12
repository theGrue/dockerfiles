FROM debian:buster

RUN apt-get update && apt-get install -y --no-install-recommends \
        bzip2 \
        ca-certificates \
        libusb-1.0-0 \
        wget \
    && rm -rf /var/lib/apt/lists/*

RUN set -ex \
    \
    && wget http://www.kryoflux.com/download/kryoflux_2.6_linux.tar.bz2 \
    && tar -xf kryoflux_2.6_linux.tar.bz2 \
    && rm kryoflux_2.6_linux.tar.bz2 \
    && mv kryoflux_2.6_linux/dtc/x86_64/dynamic/dtc /usr/local/bin/ \
    && mv kryoflux_2.6_linux/dtc/x86_64/lib* /usr/local/lib/ \
    && rm -rf kryoflux_2.6_linux/ \
    && echo /usr/local/lib >> /etc/ld.so.conf \
    && ldconfig

ENTRYPOINT [ "dtc" ]

CMD [ "-h" ]
