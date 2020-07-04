FROM debian:stretch-slim

ADD php/php-5.3.*.tar.gz /usr/local/src/

#
# Install PHP
#
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        gcc g++ make curl \
        libcurl4-openssl-dev \
        libjpeg62-turbo-dev libpng-dev libxpm-dev libfreetype6-dev \
        libicu-dev \
        libssl-dev \
        libxml2-dev \
        libxslt-dev \
        libzip-dev \
    && cd /usr/local/src/php-5.3.29 \
    && ./configure \
        --build=x86_64-linux-gnu \
        --with-config-file-path=/usr/local/etc/php \
        --with-config-file-scan-dir=/usr/local/etc/php/conf.d \
        --enable-bcmath \
        --with-curl \
        --enable-fpm --with-fpm-user=www-data --with-fpm-group=www-data --disable-cgi \
        --with-jpeg-dir --with-png-dir --with-zlib-dir --with-freetype-dir --enable-gd-native-ttf --with-gd \
        --enable-intl \
        --enable-mbstring \
        --with-openssl \
        --with-pdo-mysql \
        --enable-soap \
        --with-xsl \
        --with-libzip --enable-zip \
        build_alias=x86_64-linux-gnu \
     && make \
     && find -type f -name '*.a' -delete \
     && make install \
     && find /usr/local/bin /usr/local/sbin -type f -executable -exec strip --strip-all '{}' + || true \
     && make clean

#
# Install MSMTP for sending email
#
RUN apt-get update \
    && apt-get -y install msmtp
COPY linux/msmtprc /etc/

#
# Install Nginx
#
RUN apt-get update \
    && apt-get -y install nginx

#
# Install SSH
#
ARG SSH_PSWD=root
RUN apt-get update \
    && apt-get -y install openssh-server \
    && sed -i 's/UsePAM yes/UsePAM no/g' /etc/ssh/sshd_config \
    && sed -i 's/#ClientAliveInterval 0/ClientAliveInterval 30/g' /etc/ssh/sshd_config \
    && sed -i 's/#ClientAliveCountMax 3/ClientAliveCountMax 1200/g' /etc/ssh/sshd_config \
    && sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config \
    && sed -i 's/www-data:\/var\/www:\/usr\/sbin\/nologin/www-data:\/var\/www:\/bin\/bash/g' /etc/passwd \
    && echo "root:${SSH_PSWD}" | chpasswd \
    && echo "www-data:${SSH_PSWD}" | chpasswd
COPY linux/.bashrc /root/
COPY linux/.bashrc /var/www/
COPY linux/.profile /var/www/

#
# Rebuild file structure
#
RUN mv /var/www/html /var/www/current \
    && rm -rf /var/www/current/* \
    && chmod 0775 /var/www/current \
    && chown -R www-data:www-data /var/www
COPY --chown=www-data:www-data nginx/nginx.conf /var/www/current/

#
# Prepare for start
#
COPY docker-bootstrap /usr/local/bin/
WORKDIR /var/www
EXPOSE 22 80 9000

CMD ["docker-bootstrap"]