FROM alpine:3.10

# Install packages
RUN apk --no-cache add php7 php7-fpm php7-mysqli php7-json php7-openssl php7-curl \
    php7-zlib php7-xml php7-phar php7-intl php7-dom php7-xmlreader php7-ctype php7-session \
    php7-mbstring php7-gd nginx supervisor curl

# Configure nginx
COPY configs/nginx.conf /etc/nginx/nginx.conf

# Configure PHP-FPM
COPY configs/fpm-pool.conf /etc/php7/php-fpm.d/www.conf
COPY configs/php.ini /etc/php7/conf.d/zzz_custom.ini

# Configure supervisord
COPY configs/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Make sure files/folders needed by the processes 
# are accessable when they run under the nobody user
RUN chown -R nobody.nobody /run && \
    chown -R nobody.nobody /var/lib/nginx && \
    chown -R nobody.nobody /var/tmp/nginx && \
    chown -R nobody.nobody /var/log/nginx

# Switch to use a non-root user from here on
USER nobody

# Add application
WORKDIR /app
COPY --chown=nobody ./ /app/

# Expose php
EXPOSE 3001

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Configure a healthcheck to validate that everything is up&running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:3001/fpm-ping