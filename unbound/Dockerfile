FROM ubuntu:20.10
LABEL maintainer="JK"

SHELL ["/bin/sh", "-x", "-c"]
ENV SERIAL 2

ENV CFLAGS=-Ofast
ENV BUILD_DEPS   curl make build-essential git libevent-dev libexpat1-dev autoconf file libssl-dev byacc
ENV RUNTIME_DEPS bash util-linux coreutils findutils grep libssl1.1 ldnsutils libevent-2.1 expat ca-certificates runit runit-helper jed logrotate

RUN apt-get update; apt-get -qy dist-upgrade; apt-get -qy clean && \
    apt-get install -qy --no-install-recommends $RUNTIME_DEPS && \
    rm -fr /tmp/* /var/tmp/* /var/cache/apt/* /var/lib/apt/lists/* /var/log/apt/* /var/log/*.log

ENV UNBOUND_GIT_URL https://github.com/NLnetLabs/unbound.git
ENV UNBOUND_GIT_REVISION 74e06cc4b3fbe3dcea08eb93fcfca8f4359a9fb5

RUN update-ca-certificates 2> /dev/null || true

WORKDIR /tmp

RUN apt-get update; apt-get install -qy --no-install-recommends $BUILD_DEPS && \
    git clone --depth=1000 "$UNBOUND_GIT_URL" && \
    cd unbound && \
    git checkout "$UNBOUND_GIT_REVISION" && \
    groupadd _unbound && \
    useradd -g _unbound -s /etc -d /dev/null _unbound && \
    ./configure --prefix=/opt/unbound --with-pthreads \
    --with-username=_unbound --with-libevent && \
    make -j"$(getconf _NPROCESSORS_ONLN)" install && \
    mv /opt/unbound/etc/unbound/unbound.conf /opt/unbound/etc/unbound/unbound.conf.example && \
    apt-get -qy purge $BUILD_DEPS && apt-get -qy autoremove && \
    rm -fr /opt/unbound/share/man && \
    rm -fr /tmp/* /var/tmp/* /var/cache/apt/* /var/lib/apt/lists/* /var/log/apt/* /var/log/*.log

RUN mkdir -p \
    /etc/service/unbound \
    /etc/service/watchdog

COPY unbound.sh /etc/service/unbound/run
COPY unbound-check.sh /etc/service/unbound/check
COPY watchdog.sh /etc/service/watchdog/run

COPY entrypoint.sh /

RUN chmod 755 /entrypoint.sh && \
    chmod 755 /etc/service/unbound/run && \
    chmod 755 /etc/service/unbound/check

EXPOSE 553/udp 553/tcp

CMD ["/entrypoint.sh"]

ENTRYPOINT ["/entrypoint.sh"]
