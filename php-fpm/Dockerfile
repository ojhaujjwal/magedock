#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

FROM ojhaujjwal/magento-php-fpm:latest

#
#--------------------------------------------------------------------------
# Optional Software's Installation
#--------------------------------------------------------------------------
#
# Optional Software's will only be installed if you set them to `true`
# in the `docker-compose.yml` before the build.
#
#   - INSTALL_XDEBUG=            false
#

#####################################
# xDebug:
#####################################

ARG INSTALL_XDEBUG=true
ENV INSTALL_XDEBUG ${INSTALL_XDEBUG}
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    # Install the xdebug extention
    pecl install xdebug && \
    docker-php-ext-enable xdebug \
;fi


#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

ADD ./magento.ini /usr/local/etc/php/conf.d
ADD ./magento.pool.conf /usr/local/etc/php-fpm.d/

RUN usermod -u 1000 www-data

ARG MAGENTO_ROOT=/var/www/magento
WORKDIR ${MAGENTO_ROOT}

COPY bin/* /usr/local/bin/
ENTRYPOINT ["/usr/local/bin/entrypoint"]

CMD ["php-fpm"]

EXPOSE 9000
