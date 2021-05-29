# Inherit from the farmOS 2.x-dev image on Docker Hub.
FROM farmos/farmos:2.x-dev

# Replace exisiting farmOS codebase.
RUN rm -r /opt/drupal /var/farmOS/*
WORKDIR /var/farmOS
COPY --chown=www-data:www-data . .

# Install composer as the www-data user.
USER www-data
RUN composer config cache-dir /var/www/.composer
RUN composer install
RUN composer clear-cache

# Copy codebase into /opt/drupal.
USER root
RUN chown -R www-data:www-data /var/farmOS/web/sites
RUN cp -rp /var/farmOS /opt/drupal

WORKDIR /opt/drupal
CMD ["apache2-foreground"]
