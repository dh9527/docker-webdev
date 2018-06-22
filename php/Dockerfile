ARG PHP_VERSION
FROM php:${PHP_VERSION}-fpm-alpine

LABEL maintainer="YinLu <306677639@qq.com>"

COPY composer.phar ./

RUN OSVERSION=$(cat /etc/alpine-release) \
    && OSVERSION=${OSVERSION%.*} \
    && cp /etc/apk/repositories /etc/apk/repositories.bak \
#    改成阿里镜像源
    && echo -e "https://mirrors.aliyun.com/alpine/v${OSVERSION}/main\nhttps://mirrors.aliyun.com/alpine/v${OSVERSION}/community" > /etc/apk/repositories \
# composer
    && mv composer.phar /usr/local/bin/composer \
    \
    && set -xe \
  	&& apk add --no-cache --virtual .build-deps \
#  	&& apk add --no-cache \

    $PHPIZE_DEPS \
    libmemcached-dev \
    postgresql-dev \
    freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    imagemagick-dev \
    icu-dev \
    icu-libs \

    && docker-php-ext-install -j $(nproc) iconv mysqli pdo_mysql pdo_pgsql opcache zip bcmath \
    && docker-php-ext-configure gd --with-gd --with-freetype-dir --with-jpeg-dir --with-png-dir --enable-gd-native-ttf \
    && docker-php-ext-install -j $(nproc) gd \

    && docker-php-ext-configure mbstring --enable-mbstring \
    && docker-php-ext-install -j $(nproc) mbstring \

    && docker-php-ext-configure intl \
    && docker-php-ext-install -j $(nproc) intl \

    && pecl install redis \
#    && pecl install -o -f redis \
#    &&  echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini \
    && pecl install imagick \
    && pecl install xdebug \
    && pecl install memcached \

    && docker-php-ext-enable redis xdebug memcached imagick \

    \
	&& runDeps="$( \
		scanelf --needed --nobanner --format '%n#p' --recursive /usr/local \
			| tr ',' '\n' \
			| sort -u \
			| awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
	)" \
	&& apk add --no-cache --virtual .php-rundeps $runDeps \
	\

    && apk del .build-deps \
    && pecl update-channels \
    && rm -rf /tmp/pear ~/.pearrc

    # 时区设置
    # apk add tzdata \
    # && cp /etc/localtime /etc/localtime.bak \
    # && cp -rf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    # && apk del tzdata

#COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini
# tokenizer


# apk add build-essential libprotobuf-dev libboost-dev openssl protobuf-compiler
# pecl install mysql_xdevapi



EXPOSE 9000
CMD ["php-fpm"]