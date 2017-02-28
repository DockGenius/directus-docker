FROM php:7.1-fpm-alpine

ENV ADMIN_EMAIL=admin@localhost \
    SITE_NAME=Directus \
    ADMIN_PASSWORD=password \
    DIRECTUS_VERSION=master

# install alpine packages and php extensions
RUN apk --update --no-cache add \
	alpine-sdk \
	git \
	libtool \
  libcurl \
  curl-dev \
	bash \
	su-exec \
	mysql-client \
	freetype-dev \
	libpng-dev \
	libjpeg-turbo-dev \
	autoconf \
    libmcrypt-dev \
    libpng-dev \
    imagemagick-dev &&\
	docker-php-ext-install mcrypt && \
    docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ && \
    docker-php-ext-install gd curl fileinfo pdo pdo_mysql mysqli && \
  pecl install imagick && \
    docker-php-ext-enable imagick

# TODO: enable memcache again
# pecl install memcache && \
#   docker-php-ext-enable memcache

# install composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php -r "if (hash_file('SHA384', 'composer-setup.php') === '55d6ead61b29c7bdee5cccfb50076874187bd9f21f65d8991d46ec5cc90518f447387fb9f76ebae1fbbacf329e583e30') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" && \
    php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
    php -r "unlink('composer-setup.php');"

# tweak php config
RUN { \
        echo 'upload_max_filesize = 100M'; \
        echo 'post_max_size = 100M'; \
        echo 'cgi.fix_pathinfo=0'; \
        echo 'variables_order = \"EGPCS\"'; \
    } > /usr/local/etc/php/conf.d/php.ini

# set folder permissions
RUN chown -R www-data:www-data . && chmod g+s .

# install directus and set permissions
RUN su-exec www-data curl -sL "https://github.com/directus/directus/archive/${DIRECTUS_VERSION}.tar.gz" | su-exec www-data tar xz -C . --strip-components=1 && \
    su-exec www-data composer require aws/aws-sdk-php \
   		league/flysystem \
    	league/flysystem-aws-s3-v3 && \
    su-exec www-data composer install --no-dev --no-progress --prefer-dist && \
    su-exec www-data mkdir logs

# init script and CMD
COPY init.sh /opt/
RUN chmod +x /opt/init.sh
CMD /opt/init.sh && exec php-fpm
