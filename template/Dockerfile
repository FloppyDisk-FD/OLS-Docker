FROM ubuntu:18.04

ARG OLS_VERSION
ARG PHP_VERSION


RUN apt-get update && apt-get install apt-utils wget curl cron tzdata -y

RUN wget -O - http://rpms.litespeedtech.com/debian/enable_lst_debian_repo.sh | bash

RUN wget https://openlitespeed.org/packages/openlitespeed-$OLS_VERSION.src.tgz && \
    tar xzf openlitespeed-$OLS_VERSION.src.tgz && cd openlitespeed && ./install.sh && \
    echo 'cloud-docker' > /usr/local/lsws/PLAT && rm -rf /openlitespeed && rm /openlitespeed-$OLS_VERSION.src.tgz

RUN apt-get install mysql-client $PHP_VERSION $PHP_VERSION-common $PHP_VERSION-mysql $PHP_VERSION-opcache \
    $PHP_VERSION-curl $PHP_VERSION-json $PHP_VERSION-imagick $PHP_VERSION-redis $PHP_VERSION-memcached $PHP_VERSION-intl -y

RUN wget -O /usr/local/lsws/admin/misc/lsup.sh \
    https://raw.githubusercontent.com/litespeedtech/openlitespeed/master/dist/admin/misc/lsup.sh && \
    chmod +x /usr/local/lsws/admin/misc/lsup.sh && \
    ln -s /usr/local/lsws/$PHP_VERSION/bin/php /usr/bin/php

RUN wget -O -  https://get.acme.sh | sh

EXPOSE 7080

ADD docker.conf /usr/local/lsws/conf/templates/docker.conf
ADD setup_docker.sh /usr/local/lsws/bin/setup_docker.sh
ADD httpd_config.xml /usr/local/lsws/conf/httpd_config.xml
ADD htpasswd /usr/local/lsws/admin/conf/htpasswd

RUN /usr/local/lsws/bin/setup_docker.sh && rm /usr/local/lsws/bin/setup_docker.sh
RUN chown 999:999 /usr/local/lsws/conf -R
RUN cp -RP /usr/local/lsws/conf/ /usr/local/lsws/.conf/
RUN cp -RP /usr/local/lsws/admin/conf /usr/local/lsws/admin/.conf/
RUN ln -sf /usr/local/lsws/$PHP_VERSION/bin/lsphp /usr/local/lsws/fcgi-bin/lsphp7
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
WORKDIR /var/www/vhosts/
