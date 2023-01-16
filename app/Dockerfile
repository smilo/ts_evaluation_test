ARG PHP_VERSION=7.3.27-fpm-buster

FROM php:${PHP_VERSION}

ARG app_user=app-user
ARG app_home=/var/www

# update OS, install newrelic agent, create app user, give right permissions to app user
RUN apt-get update && \
    groupadd -g 1000 ${app_user} && \
    useradd -u 1000 -ms /bin/bash -g ${app_user} ${app_user} && \
    chown ${app_user}:${app_user} /usr/local/var/log && \
    mkdir /var/log/newrelic &&\
    chown ${app_user}:${app_user} /var/log/newrelic &&\
    export FILE=$(curl -sS https://download.newrelic.com/php_agent/release/ | sed -n 's/.*>\(.*linux\).tar.gz<.*/\1/p') && \
    curl -L "https://download.newrelic.com/php_agent/release/${FILE}.tar.gz" | tar -C /tmp -zx && \
    export NR_INSTALL_USE_CP_NOT_LN=1 && \
    export NR_INSTALL_SILENT=1 && \
    /tmp/newrelic-php5-*/newrelic-install install && \
    rm -rf /tmp/newrelic-php5-* /tmp/nrinstall*

COPY --chown=${app_user}:${app_user} entrypoint.sh /entrypoint.sh
RUN  chmod 0700 /entrypoint.sh
COPY --chown=${app_user}:${app_user} . ${app_home}

WORKDIR ${app_home}
USER ${app_user}

EXPOSE 9000
ENTRYPOINT ["/entrypoint.sh"]