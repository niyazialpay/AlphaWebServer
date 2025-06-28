FROM dunglas/frankenphp:builder AS builder

COPY --from=caddy:builder /usr/bin/xcaddy /usr/bin/xcaddy

RUN CGO_ENABLED=1 \
    XCADDY_SETCAP=1 \
    XCADDY_GO_BUILD_FLAGS="-ldflags='-w -s' -tags=nobadger,nomysql,nopgx" \
    CGO_CFLAGS=$(php-config --includes) \
    CGO_LDFLAGS="$(php-config --ldflags) $(php-config --libs)" \
    xcaddy build \
        --output /usr/local/bin/frankenphp \
        --with github.com/dunglas/frankenphp=./ \
        --with github.com/dunglas/frankenphp/caddy=./caddy/ \
        --with github.com/dunglas/caddy-cbrotli \
        --with github.com/dunglas/mercure/caddy \
        --with github.com/dunglas/vulcain/caddy \
        --with github.com/caddy-dns/cloudflare \
        --with github.com/WeidiDeng/caddy-cloudflare-ip \
        --with github.com/Javex/caddy-fail2ban@main \
        --with github.com/lanrat/caddy-dynamic-remoteip

FROM dunglas/frankenphp

COPY --from=builder /usr/local/bin/frankenphp /usr/local/bin/frankenphp

RUN install-php-extensions \
        pdo_mysql \
        gd \
        intl \
        zip \
        opcache \
        mbstring \
        memcached \
        redis \
        imagick \
        raphf \
        http \
        bcmath \
        mongodb \
        mysqli

RUN curl -sS https://getcomposer.org/installer | php -- \
      --install-dir=/usr/local/bin --filename=composer