FROM debian:jessie

MAINTAINER Abel Aguiar <abel.prog@gmail.com>

RUN dpkg-divert --local --rename --add /sbin/initctl && ln -sf /bin/true /sbin/initctl

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y wget && mkdir /src

RUN echo "deb http://packages.dotdeb.org jessie all" > /etc/apt/sources.list.d/dotdeb.list \
    && echo "deb-src http://packages.dotdeb.org jessie all" >> /etc/apt/sources.list.d/dotdeb.list \
    && wget -O - http://www.dotdeb.org/dotdeb.gpg |apt-key add - \
    && apt-get update && apt-get install --no-install-recommends --no-install-suggests -y php5-cli \
    php5-fpm php5-mysql php5-intl php5-xdebug php5-recode php5-mcrypt php5-memcache \
    php5-memcached php5-imagick php5-curl php5-xsl php5-dev php5-tidy php5-xmlrpc \
    php5-gd php5-pspell nginx

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('SHA384', 'composer-setup.php') === 'e115a8dc7871f15d853148a7fbac7da27d6c0030b848d9b3dc09e2a0388afed865e6a3d6b3c0fad45c48e2b5fc1196ae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');"

RUN sed -i "s/www-data;/www-data;\\ndaemon off;/g" /etc/nginx/nginx.conf \
    && sed -i "s/short_open_tag = On/short_open_tag = Off/" /etc/php5/cli/php.ini \
    && sed -i "s/short_open_tag = On/short_open_tag = Off/" /etc/php5/fpm/php.ini \
    && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL/" /etc/php5/cli/php.ini \
    && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL/" /etc/php5/fpm/php.ini \
    && sed -i "s/display_errors = Off/display_errors = On/" /etc/php5/cli/php.ini \
    && sed -i "s/display_errors = Off/display_errors = On/" /etc/php5/fpm/php.ini \
    && sed -i "s/display_startup_errors = Off/display_startup_errors = On/" /etc/php5/cli/php.ini \
    && sed -i "s/display_startup_errors = Off/display_startup_errors = On/" /etc/php5/fpm/php.ini

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

RUN rm -rf /var/lib/apt/lists/* \
    && apt-get clean

RUN echo "#!/bin/bash\n/etc/init.d/php5-fpm restart && nginx" >> run.sh
RUN chmod a+x /run.sh

EXPOSE 80 443

WORKDIR /src

ENTRYPOINT ["/run.sh"]