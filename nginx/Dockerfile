FROM nginx:1.10-alpine

ARG PHP_UPSTREAM=php-fpm

ADD nginx.conf /etc/nginx/
ADD magento.conf /etc/nginx/conf.d/magento.conf

ARG PUID=1000
ARG PGID=1000
ARG MAGENTO_ROOT=/var/www/magento
ENV MAGENTO_ROOT ${MAGENTO_ROOT}

RUN set -x \
  && addgroup -g $PGID -S www-data \
  && adduser -u $PGID -D -S -G www-data www-data  

RUN rm /etc/nginx/conf.d/default.conf \
    && echo "upstream php-upstream { server ${PHP_UPSTREAM}:9000; }" > /etc/nginx/conf.d/upstream.conf

COPY bin/* /usr/local/bin/
ENTRYPOINT ["/usr/local/bin/entrypoint"]

CMD ["nginx"]

EXPOSE 80 443