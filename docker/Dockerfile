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

# Configure composer to use a github access token for accessing private repos.
#RUN --mount=type=secret,id=github_access_token,uid=1000 \
#  composer config github-oauth.github.com $(cat /run/secrets/github_access_token)

# Install composer.
RUN composer config cache-dir $(mktemp -d)
RUN composer install --no-dev
RUN composer clear-cache

# Copy codebase into /opt/drupal.
RUN chown -R www-data:www-data /var/farmOS/web/sites
RUN cp -rp /var/farmOS /opt/drupal

WORKDIR /opt/drupal
CMD ["apache2-foreground"]
