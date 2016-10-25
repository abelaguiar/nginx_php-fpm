FROM debian:jessie
MAINTAINER Ian Rodrigues <ianrodrigues@gmail.com>

RUN dpkg-divert --local --rename --add /sbin/initctl && ln -sf /bin/true /sbin/initctl

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y wget && mkdir /src

RUN echo "deb http://packages.dotdeb.org jessie all" > /etc/apt/sources.list.d/dotdeb.list \
    && echo "deb-src http://packages.dotdeb.org jessie all" >> /etc/apt/sources.list.d/dotdeb.list \
    && wget -O - http://www.dotdeb.org/dotdeb.gpg |apt-key add - \
    && apt-get update && apt-get install --no-install-recommends --no-install-suggests -y php7.0-cli \
    php7.0-fpm php7.0-mysql php7.0-intl php7.0-xdebug php7.0-recode php7.0-mcrypt php7.0-memcache \
    php7.0-memcached php7.0-imagick php7.0-curl php7.0-xsl php7.0-dev php7.0-tidy php7.0-xmlrpc \
    php7.0-gd php7.0-pspell php7.0-mbstring php7.0-zip nginx

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('SHA384', 'composer-setup.php') === 'e115a8dc7871f15d853148a7fbac7da27d6c0030b848d9b3dc09e2a0388afed865e6a3d6b3c0fad45c48e2b5fc1196ae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');"

RUN sed -i "s/www-data;/www-data;\\ndaemon off;/g" /etc/nginx/nginx.conf \
    && sed -i "s/short_open_tag = On/short_open_tag = Off/" /etc/php/7.0/cli/php.ini \
    && sed -i "s/short_open_tag = On/short_open_tag = Off/" /etc/php/7.0/fpm/php.ini \
    && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL/" /etc/php/7.0/cli/php.ini \
    && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL/" /etc/php/7.0/fpm/php.ini \
    && sed -i "s/display_errors = Off/display_errors = On/" /etc/php/7.0/cli/php.ini \
    && sed -i "s/display_errors = Off/display_errors = On/" /etc/php/7.0/fpm/php.ini \
    && sed -i "s/display_startup_errors = Off/display_startup_errors = On/" /etc/php/7.0/cli/php.ini \
    && sed -i "s/display_startup_errors = Off/display_startup_errors = On/" /etc/php/7.0/fpm/php.ini

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

RUN rm -rf /var/lib/apt/lists/* \
    && apt-get clean

RUN echo "#!/bin/bash\n/etc/init.d/php7.0-fpm restart && nginx" >> run.sh
RUN chmod a+x /run.sh

EXPOSE 80 443

WORKDIR /src

ENTRYPOINT ["/run.sh"]