FROM ubuntu

LABEL maintainer="Júlio César <julio.souzam@gmail.com.br>"

RUN dpkg-divert --local --rename --add /sbin/initctl && ln -sf /bin/true /sbin/initctl

ENV DEBIAN_FRONTEND noninteractive

RUN apt update && apt install -y ca-certificates apt-transport-https wget

RUN apt install --no-install-recommends --no-install-suggests -y php7.2-fpm php7.2-cli php7.2-mysql php7.2-intl php7.2-recode \
    php7.2-mbstring php7.2-curl php7.2-xsl php7.2-tidy php7.2-pgsql php7.2-sqlite3 php7.2-pspell php7.2-gd php7.2-zip nginx

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');"

RUN sed -i "s/www-data;/www-data;\\ndaemon off;/g" /etc/nginx/nginx.conf \
    && sed -i "s/short_open_tag = On/short_open_tag = Off/" /etc/php/7.2/cli/php.ini \
    && sed -i "s/short_open_tag = On/short_open_tag = Off/" /etc/php/7.2/fpm/php.ini \
    && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL/" /etc/php/7.2/cli/php.ini \
    && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL/" /etc/php/7.2/fpm/php.ini \
    && sed -i "s/display_errors = Off/display_errors = On/" /etc/php/7.2/cli/php.ini \
    && sed -i "s/display_errors = Off/display_errors = On/" /etc/php/7.2/fpm/php.ini \
    && sed -i "s/display_startup_errors = Off/display_startup_errors = On/" /etc/php/7.2/cli/php.ini \
    && sed -i "s/display_startup_errors = Off/display_startup_errors = On/" /etc/php/7.2/fpm/php.ini

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

RUN echo "#!/bin/bash\n/etc/init.d/php7.2-fpm restart && nginx" >> run.sh
RUN chmod a+x /run.sh

EXPOSE 80 443

WORKDIR /var/www/html

ENTRYPOINT ["/run.sh"]