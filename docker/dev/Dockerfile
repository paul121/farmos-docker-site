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

# Configure PHP CodeSniffer.
# @todo This only sets the installed path in /var/farmOS which might not be copied to /opt/drupal.
RUN { \
    echo '<?xml version="1.0" encoding="UTF-8"?>'; \
    echo '<ruleset name="farmOS">'; \
    echo '  <description>PHP CodeSniffer configuration for farmOS development.</description>'; \
    echo '  <file>.</file>'; \
    echo '  <arg name="extensions" value="php,module,inc,install,test,profile,theme,css,info,txt,yml"/>'; \
    echo '  <config name="drupal_core_version" value="9"/>'; \
    echo '  <rule ref="Drupal">'; \
    echo '    <exclude name="Drupal.InfoFiles.AutoAddedKeys.Version"/>'; \
    echo '    <exclude name="Drupal.Arrays.Array.LongLineDeclaration"/>'; \
    # @todo https://www.drupal.org/project/coder/issues/2159253
    echo '    <exclude name="Drupal.Commenting.InlineComment.SpacingAfter"/>'; \
    echo '  </rule>'; \
    echo '  <rule ref="DrupalPractice">'; \
    # @todo https://www.drupal.org/project/coder/issues/2159253
    echo '    <exclude name="DrupalPractice.Commenting.CommentEmptyLine.SpacingAfter"/>'; \
    echo '  </rule>'; \
    echo '  <rule ref="Internal.Tokenizer.Exception"><severity>0</severity></rule>'; \
    echo '</ruleset>'; \
  } > /var/farmOS/phpcs.xml \
  && /var/farmOS/vendor/bin/phpcs --config-set installed_paths /var/farmOS/vendor/drupal/coder/coder_sniffer

# Configure PHPUnit.
# @todo This is only set in /var/farmOS which might not be copied to /opt/drupal.
RUN cp -p /var/farmOS/web/core/phpunit.xml.dist /var/farmOS/phpunit.xml \
  && sed -i 's|bootstrap="tests/bootstrap.php"|bootstrap="web/core/tests/bootstrap.php"|g' /var/farmOS/phpunit.xml \
  && sed -i 's|name="SIMPLETEST_BASE_URL" value=""|name="SIMPLETEST_BASE_URL" value="http://localhost"|g' /var/farmOS/phpunit.xml \
  && sed -i 's|name="SIMPLETEST_DB" value=""|name="SIMPLETEST_DB" value="pgsql://farm:farm@db/farm"|g' /var/farmOS/phpunit.xml \
  && sed -i 's|name="BROWSERTEST_OUTPUT_DIRECTORY" value=""|name="BROWSERTEST_OUTPUT_DIRECTORY" value="/var/www/html/sites/simpletest/browser_output"|g' /var/farmOS/phpunit.xml \
  && sed -i 's|\./|\./web/core/|g' /var/farmOS/phpunit.xml \
  && sed -i 's|\.\./web/core/|\./web/|g' /var/farmOS/phpunit.xml \
  && sed -i 's|  </php>|    <env name="SYMFONY_DEPRECATIONS_HELPER" value="quiet[]=indirect"/>'"\n"'  </php>|g' /var/farmOS/phpunit.xml \
  && mkdir -p /var/farmOS/web/sites/simpletest/browser_output

# Copy codebase into /opt/drupal.
USER root
RUN chown -R www-data:www-data /var/farmOS/web/sites
RUN cp -rp /var/farmOS /opt/drupal

WORKDIR /opt/drupal
CMD ["apache2-foreground"]
