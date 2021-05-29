# farmos-docker-site
An example repository for maintaining a farmOS docker image of a custom site.

## Development

- Bring up the development containers. This will build the development image
  if it doesn't exist.

```
docker-compose up -d
```

- The `web/modules/custom` directory is bind mounted into the container
  which will override all the codebase that was built inside the image. We
  need to build the code base again with composer. *TODO: Can this be
  improved?*

```
docker-compose exec -u www-data www composer install
```

- With the codebase built locally you can begin developing custom modules in
  the `web/modules/custom` directory. Commit any changes made to these files.


## Configuration

Site configuration can be exported and imported between different environments.
This example assumes that configuration is checked into this repository after
being exported from a development environment. The configuration is then built
into docker images that can be deployed to production where configuration can
then be imported.

NOTE: This example saves configuration outside the web root in the `config`
directory. This is the preferred method, but requires changing the
`$settings['CONFIG_SYNC_DIRECTORY']` variable in `settings.php`. For more
information see [changing the storage location of the sync directory](https://www.drupal.org/docs/configuration-management/changing-the-storage-location-of-the-sync-directory)

Also see [managing your site's configuration](https://www.drupal.org/docs/configuration-management/managing-your-sites-configuration) and
  management [using Drush](https://www.drupal.org/node/2416591).
