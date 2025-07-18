FROM ubuntu:latest

# 1) Tüm kurulumları interaktif olmayan moda al, timezone'u ayarla
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Europe/Istanbul

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      tzdata \
      software-properties-common \
      ca-certificates \
      apt-transport-https \
      gnupg \
      dirmngr \
      curl \
      unzip \
      supervisor \
      pkg-config \
      curl \
      wget \
      apache2 \
      apache2-utils \
      ssl-cert \
 && add-apt-repository ppa:ondrej/php -y \
 && apt-get update

# 2) PHP 8.0–8.4 ve modüller (özet)
RUN apt-get install -y --no-install-recommends \
      php8.0-fpm php8.0-cli php8.0-common php8.0-zip php8.0-mysql php8.0-mbstring php8.0-tokenizer php8.0-xml php8.0-gd php8.0-curl php8.0-intl php8.0-bcmath php8.0-gmp php8.0-memcached php8.0-redis php8.0-imagick php8.0-mongodb php8.0-raphf php8.0-http \
      php8.1-fpm php8.1-cli php8.1-common php8.1-zip php8.1-mysql php8.1-mbstring php8.1-tokenizer php8.1-xml php8.1-gd php8.1-curl php8.1-intl php8.1-bcmath php8.1-gmp php8.1-memcached php8.1-redis php8.1-imagick php8.1-mongodb php8.1-raphf php8.1-http \
      php8.2-fpm php8.2-cli php8.2-common php8.2-zip php8.2-mysql php8.2-mbstring php8.2-tokenizer php8.2-xml php8.2-gd php8.2-curl php8.2-intl php8.2-bcmath php8.2-gmp php8.2-memcached php8.2-redis php8.2-imagick php8.2-mongodb php8.2-raphf php8.2-http \
      php8.3-fpm php8.3-cli php8.3-common php8.3-zip php8.3-mysql php8.3-mbstring php8.3-tokenizer php8.3-xml php8.3-gd php8.3-curl php8.3-intl php8.3-bcmath php8.3-gmp php8.3-memcached php8.3-redis php8.3-imagick php8.3-mongodb php8.3-raphf php8.3-http \
      php8.4-fpm php8.4-cli php8.4-common php8.4-zip php8.4-mysql php8.4-mbstring php8.4-tokenizer php8.4-xml php8.4-gd php8.4-curl php8.4-intl php8.4-bcmath php8.4-gmp php8.4-memcached php8.4-redis php8.4-imagick php8.4-mongodb php8.4-raphf php8.4-http \
    python3 python3-pip \
    && rm -rf /var/lib/apt/lists/*

# 3) Supervisor konfigürasyonu ve Composer
COPY supervisor.d/php-fpm.conf /etc/supervisor/conf.d/php-fpm.conf
RUN curl -sS https://getcomposer.org/installer | php -- \
      --install-dir=/usr/local/bin --filename=composer

# Provide 'python' alias for python3
RUN ln -sf /usr/bin/python3 /usr/bin/python

# Install Go from official binary
ARG GO_VERSION=1.24.4
RUN curl -fsSL https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz | tar -C /usr/local -xz \
    && ln -s /usr/local/go/bin/go /usr/local/bin/go \
    && ln -s /usr/local/go/bin/gofmt /usr/local/bin/gofmt

ENV PATH=$PATH:/usr/local/go/bin


RUN curl -fsSL https://deb.nodesource.com/setup_24.x | bash - \
 && apt-get install -y nodejs


# Install code-server
ARG CODE_SERVER_VERSION=4.101.1
RUN curl -fsSL https://github.com/coder/code-server/releases/download/v${CODE_SERVER_VERSION}/code-server_${CODE_SERVER_VERSION}_amd64.deb -o code-server.deb \
 && dpkg -i code-server.deb \
 && rm code-server.deb

# --- suppress Supervisor root warning by specifying a user ---
RUN sed -i '/^\[supervisord\]/a user=root' /etc/supervisor/supervisord.conf

RUN a2enmod ssl socache_shmcb proxy proxy_fcgi setenvif headers remoteip rewrite

COPY user-entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh

WORKDIR /var/www/vhosts
EXPOSE 9000 80 443 8080
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
CMD ["supervisord", "-n"]
