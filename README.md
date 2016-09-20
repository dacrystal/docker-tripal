# Tripal Docker Image

This image contains Tripal 2.1 & Chado 1.3. The image is based on [docker-drupal](https://github.com/samos123/docker-drupal/blob/master/README.md).
It will automatically setup the database and install a default site.

The image doesn't contain a database so you have to create a seperate database
container (which is no effort if you use the provided configuration for
*docker-compose*) and link this container or pass the database information.

## Usage

`docker-compose.yml`.

```yaml
version: '2'
services:
  web:
    build: .
    links:
      - db:postgres
    volumes:
      - /var/www/html/sites
      - /var/www/private
    environment:
      - UPLOAD_LIMIT=20M
      - MEMORY_LIMIT=128M
    ports:
      - "3000:80"

  db:
    image: postgres
    environment:
      - POSTGRES_PASSWORD=password
    volumes:
      - /var/lib/postgresql/data

```

You can use [Docker-Compose](https://docs.docker.com/compose/) in a directory
that contains the provided [`docker-compose.yml`](https://github.com/samos123/docker-drupal/blob/master/docker-compose.yml):

    docker-compose up

This will launch a new tripal site with a default modules. If you want custom
modules, see *Customization*.

As customizations and uploads are stored you must take care of these directories
if you want to keep these:
  - `/var/www/html/sites` (modules, themes, uploaded files)
  - `/var/www/private` (non-public files, e.g. to store backups)

As these folders are defined as *volumes* in the sample `docker-compose.yml`,
you can easily update your container to use the latest image while preserving
any modifications with:

    docker-compose pull && docker-compose up -d


## Tripal options:

  - `TRIPAL_DOWNLOAD_MODULES`: additional drupal modules; e.g "tripal_analysis_blast-7.x-2.x-dev"
  - `TRIPAL_GIT_CLONE_MODULES`: additional drupal modules from a git repo; e.g "https://github.com/tripal/tripal_analysis_expression.git"
  - `TRIPAL_ENABLE_MODULES` : default to "tripal_genetic tripal_natural_diversity tripal_phenotype tripal_project tripal_pub tripal_stock"


## Database options(`samos123/docker-drupal`)

You can use a linked database-container with the alias `postgres` as shown
above - Drupal will be automatically configured to it. Or you use an external
database-host. Therefore pass the following environment variables to your
container:

  - `DB_DRIVER`
  - `DB_HOST`
  - `DB_PORT`
  - `DB_NAME`
  - `DB_USER`
  - `DB_PASS`


## Other options(`samos123/docker-drupal`)

  - `VIRTUAL_HOST` - sets the `ServerName`-directive for *httpd* and *Drupal*'s
    `base_url` configuration variable; handy in conjunction with
    [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)
    - if it is a comma-seperated list, the first value is used
  - `SERVERNAME` - use this to explicitly set httpd's `ServerName`-directive
    - if none of these both variables are given, the `/etc/hostname` will be
      used
  - `BASE_URL` - explicitly set the `base_url` configuration variable for
    *Drupal*
    - trailing slashes are not allowed
  - `BASE_URL_PROTO` (default: `https://`) - if `BASE_URL` is derived from
    `VIRTUAL_HOST`, this will be prefixed as protocol
  - `UPLOAD_LIMIT` (default: `10M`) - sets variables for the *PHP*-interpreter
    to control maximum upload sizes
  - `MEMORY_LIMIT` (default: `64M`) - sets the [`memory_limit`](http://php.net/manual/en/ini.core.php#ini.memory-limit)
     for the *PHP*-interpreter
  - `ADMIN_USER` (default: `admin`) - sets the administrator account name when
    creating a new instance
  - `ADMIN_PASSWORD` - (default: `changeme`) sets the administrator password when
    creating a new instance.


## Customization
See the [samos123/docker-drupal Customization](https://github.com/samos123/docker-drupal/blob/master/README.md#customization)


## Links

Source code: [https://github.com/dacrustal/docker-tripal](https://github.com/dacrystal/docker-tripal)

Registry url: [https://registry.hub.docker.com/u/dacrystal/tripal/](https://registry.hub.docker.com/u/dacrystal/tripal/)

**samos123/docker-drupal** : See the [samos123/docker-drupal Credits](https://github.com/samos123/docker-drupal/blob/master/README.md#Credits)


# TODO #

- [ ] Include postgres server in the image
- [ ] Only postgres should be allowed (required for installation of the 
Chado Schema)


