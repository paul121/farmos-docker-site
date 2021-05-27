# Inherit from the farmOS 2.x image on Docker Hub.
FROM farmos/farmos:2.x

# Increase upload filesize.
RUN { \
    echo 'upload_max_filesize=25M'; \
    echo 'post_max_size=25M'; \
  } >> /usr/local/etc/php/conf.d/farmOS-recommended.ini

# Replace exisiting farmOS codebase.
RUN rm -r /opt/drupal /var/farmOS/* && chown www-data:www-data /var/farmOS
WORKDIR /var/farmOS
COPY --chown=www-data:www-data . .

# Create composer cache with permissions for www-data.
RUN composer config cache-dir $(mktemp -d) && chown -R www-data:www-data $(composer config cache-dir)

# Install composer as the www-data user.
USER www-data
RUN composer install --no-dev
RUN composer clear-cache

# Copy codebase into /opt/drupal.
USER root
RUN cp -rp /var/farmOS /opt/drupal

CMD ["apache2-foreground"]
