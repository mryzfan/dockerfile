FROM alpine
MAINTAINER Shengjing Zhu <i@zhsj.me>

RUN set -ex \
    && apk upgrade --update \
    && apk add --virtual .sks-deps db-utils libgcc gmp s6 \
    && apk add --virtual .sks-builddeps \
        build-base opam curl db-dev patch m4 perl gmp-dev zlib-dev \
    && curl -sSL https://bitbucket.org/skskeyserver/sks-keyserver/get/default.tar.gz | tar xz \
    && mv skskeyserver* sks-keyserver \
    && opam init --no-setup -y \
    && opam install -y cryptokit \
    && export PATH=~/.opam/system/bin/:$PATH \
    && cd sks-keyserver \
        && sed 's/db-.*/db-5.3/' Makefile.local.unused > Makefile.local \
        && sed -i '/warn-error/d' Makefile \
        && make dep \
        && make sks \
        && strip sks \
        && install -m755 sks /usr/sbin/sks \
    && cd .. \
    && apk --purge del .sks-builddeps \
    && rm -rf ~/.opam \
    && rm -rf /var/cache/apk/* \
    && rm -rf sks-keyserver \
    && mkdir -p /var/lib/sks

ADD files /usr/local/
VOLUME /var/lib/sks
EXPOSE 11371 11370
CMD ["s6-svscan", "/usr/local/etc/s6"]
