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
