FROM alpine:3.15

ENV LANG=zh_CN.UTF-8 \
    TZ=Asia/Shanghai \
    WORKDIR=/var/www \
    PS1="\u@\h:\w \$ "
    
ADD https://github.com/just-containers/s6-overlay/releases/download/v1.22.1.0/s6-overlay-nobin.tar.gz /tmp/
RUN apk add --no-cache curl tar \
    && curl -sSL "https://github.com/just-containers/s6-overlay/releases/download/v2.2.0.3/s6-overlay-$(uname -m | sed s/x86_64/amd64/).tar.gz" | tar -xzf - -C / \
    && apk add --no-cache --update \
       bash \
       git \
       tzdata \
       shadow \
       caddy \
       php7 \
       php7-fpm \
       php7-curl \
       php7-json \
       php7-mbstring \
       php7-mysqli \
       php7-zip \
       php7-gd \
       php7-session \
    && ln -sf /usr/share/zoneinfo/${TZ} /etc/localtime \
    && echo -e "${TZ}" > /etc/timezone \
    && echo -e "max_execution_time = 3600\nupload_max_filesize=128M\npost_max_size=128M\nmemory_limit=1024M\ndate.timezone=${TZ}" > /etc/php7/conf.d/99-overrides.ini \
    && echo -e "[global]\nerror_log = /dev/stdout\ndaemonize = no\ninclude=/etc/php7/php-fpm.d/*.conf" > /etc/php7/php-fpm.conf \
    && echo -e "[www]\nuser = caddy\ngroup = caddy\nlisten = 127.0.0.1:9000\nlisten.owner = caddy\nlisten.group = caddy\npm = ondemand\npm.max_children = 75\npm.max_requests = 500\npm.process_idle_timeout = 10s\nchdir = $WORKDIR" > /etc/php7/php-fpm.d/www.conf \
    && echo -e ":8080\nroot * $WORKDIR\nlog {\n    level warn\n}\nphp_fastcgi 127.0.0.1:9000\nfile_server" > /etc/caddy/Caddyfile \
    && rm -rf \
       $WORKDIR/* \
       /var/cache/apk/* \
       /tmp/* \
    && git clone --depth=1 -b master https://github.com/MoeNetwork/Tieba-Cloud-Sign $WORKDIR \
    && cp -r $WORKDIR/docker/s6-overlay/etc/cont-init.d/* /etc/cont-init.d \
    && cp -r $WORKDIR/docker/s6-overlay/etc/services.d/* /etc/services.d
WORKDIR $WORKDIR
ENTRYPOINT ["/init"]
