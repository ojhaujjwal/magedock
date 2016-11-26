#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

FROM ojhaujjwal/magento-php-cli:latest

ARG MAGENTO_ROOT=/var/www/magento
ENV MAGENTO_ROOT ${MAGENTO_ROOT}

# Run utils
RUN apt-get update && \
    apt-get install -y --force-yes \
        xz-utils \
        git \
        curl \
    && apt-get clean

#
#--------------------------------------------------------------------------
# Optional Software's Installation
#--------------------------------------------------------------------------
#
# Optional Software's will only be installed if you set them to `true`
# in the `docker-compose.yml` before the build.
#
#   - INSTALL_XDEBUG=           false
#   - COMPOSER_GLOBAL_INSTALL=  false
#   - INSTALL_NODE=             false
#

#####################################
# Nodejs:
#####################################

# Check if Nodejs needs to be installed
ARG INSTALL_NODE=true
ENV INSTALL_NODE ${INSTALL_NODE}

# Install node js from apt
# when installing from a package manager the bin is called nodejs 
# so we need to symlink
RUN if [ ${INSTALL_NODE} = true ]; then \
    apt-get update && \
    apt-get install -y nodejs npm && \
    ln -s /usr/bin/nodejs /usr/bin/node && \
    npm install -g bower \
;fi

#####################################
# xDebug:
#####################################

# Check if xDebug needs to be installed
ARG INSTALL_XDEBUG=true
ENV INSTALL_XDEBUG ${INSTALL_XDEBUG}
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    # Load the xdebug extension only with phpunit commands
    apt-get install -y --force-yes php7.0-xdebug && \
    sed -i 's/^/;/g' /etc/php/7.0/cli/conf.d/20-xdebug.ini && \
    echo "alias phpunit='php -dzend_extension=xdebug.so ${MAGENTO_ROOT}/vendor/bin/phpunit'" >> ~/.bashrc \
;fi


#####################################
# Non-Root User:
#####################################

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ARG PGID=1000
RUN groupadd -g $PGID magento && \
    useradd -u $PUID -g magento -m magento   
    
#####################################
# Composer:
#####################################

# Setup COMPOSER_HOME directory and Add the composer.json
COPY ./composer.json /home/magento/.composer/composer.json
# Fix root ownership of config.json and auth.json
ENV COMPOSER_HOME=/home/magento/.composer/

# Make sure that ~/.composer belongs to magento
RUN chown -R magento:magento /home/magento/.composer
USER magento

# Check if global install need to be ran
ARG COMPOSER_GLOBAL_INSTALL=true
ENV COMPOSER_GLOBAL_INSTALL ${COMPOSER_GLOBAL_INSTALL}
RUN if [ ${COMPOSER_GLOBAL_INSTALL} = true ]; then \
    # run the install
    composer global install \
;fi



#PHP.ini settings
ADD ./magento.ini /etc/php/7.0/cli/conf.d

USER root

# Add crontab file in the cron directory
ADD magento-cron /var/tmp/magento-cron


# Check if cron needs to be run
ARG CRON
ENV CRON ${CRON}
RUN if [ ${CRON} = true ]; then \
    cp /var/tmp/magento-cron /etc/cron.d/ && \
    sed -i "s%MAGENTO_ROOT%${MAGENTO_ROOT}%g" /etc/cron.d/magento-cron && \
    chmod 600 /etc/cron.d/magento-cron \
;fi

#Add container startup script
RUN mkdir -p /etc/my_init.d
ADD bin/docker-environment /etc/my_init.d/docker-environment

# Magento commands:
COPY bin/* /usr/local/bin/

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENTRYPOINT ["/usr/local/bin/docker-environment"]
CMD ["/sbin/my_init"]

# Set default work directory
WORKDIR ${MAGENTO_ROOT}
