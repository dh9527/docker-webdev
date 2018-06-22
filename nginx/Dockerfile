FROM nginx:alpine

LABEL maintainer="YinLu <306677639@qq.com>"

COPY nginx.conf /etc/nginx/

#RUN apk update \
#    && apk upgrade \
#    && apk add --no-cache bash \
#    && adduser -D -H -u 1000 -s /bin/bash www-data

RUN addgroup -g 82 -S www-data \
    && adduser -u 82 -D -S -G www-data www-data \
    && rm /etc/nginx/conf.d/default.conf

VOLUME /etc/nginx/conf.d/upstream.conf

CMD ["nginx"]

EXPOSE 80 443
