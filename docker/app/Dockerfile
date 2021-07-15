FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

# ミドルウェアインストール
RUN apt-get update && apt-get install -y \
        tzdata vim \
        apache2 apache2-utils apache2-ssl-dev \
        libpng-dev \
        mariadb-client \
        php7.4 php7.4-cli php7.4-common php7.4-curl php7.4-gd php7.4-mysql php7.4-xml \
        php7.4-xmlrpc php7.4-mbstring php7.4-intl php7.4-zip php7.4-xdebug php7.4-bz2 php7.4-dba \
        php7.4-json php7.4-readline php7.4-opcache libfreetype6-dev \
        && apt remove -y libmariadb-dev-compat libmariadb-dev \
    && chown -R www-data:www-data /var/www/html \
    && cp /usr/share/zoneinfo/Asia/Tokyo /etc/localtime \
    && echo "alias ll='ls -la'" >> ${HOME}/.bashrc
    
# 設定ファイルをdockerコンテナ内のPHP、Apacheに読み込ませる
# ADD php.ini /usr/local/etc/php/
ADD 000-default.conf /etc/apache2/sites-enabled/
ADD default-ssl.conf /etc/apache2/sites-enabled/
ADD ssl/localdomain.local.pem /etc/ssl/certs/
ADD ssl/localdomain.local-key.pem /etc/ssl/private/
ADD ssl/renewal.pem     /etc/ssl/certs/
ADD ssl/renewal-key.pem /etc/ssl/private/

# Composerのインストール
RUN cd /usr/bin && curl -s http://getcomposer.org/installer | php && ln -s /usr/bin/composer.phar /usr/bin/composer \
&& sed -i  's/^\;date\.timezone\ =/date.timezone = Asia\/Tokyo/'  /etc/php/7.4/apache2/php.ini \
&& chmod 400 /etc/ssl/certs/localdomain.local.pem \
&& chmod 400 /etc/ssl/private/localdomain.local-key.pem \
&& chmod 400 /etc/ssl/certs/renewal.pem \
&& chmod 400  /etc/ssl/private/renewal-key.pem 

# modRewriteを有効化する
RUN mv /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled \
&& /bin/sh -c a2enmod rewrite \
&& echo "ServerName ubuntu" > /etc/apache2/conf-available/fqdn.conf \
&& a2enconf fqdn.conf \
&& a2enmod ssl

EXPOSE 80 443
CMD ["apache2ctl", "-D", "FOREGROUND"]
