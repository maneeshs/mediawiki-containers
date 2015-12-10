# mediawiki-docker-compose

A prototype docker-compose setup for a fully-featured MediaWiki install
including VisualEditor, Parsoid, RESTBase, Mathoid & other services.

## Requirements 

You need `docker` and `docker-compose`, which is available in recent distros
like Debian Sid. Alternatively, you can follow [the Docker install
instructions](https://docs.docker.com/compose/install/) for your distribution.

The minimum hardware requirements are a KVM or similar VM with 512M RAM. These
can be had from a variety of vendors for around $5/month. [This
comparison from ServerBear lists some
options](http://serverbear.com/compare?Sort=BearScore&Order=desc&Server+Type=VPS&Monthly+Cost=-&HDD=-&RAM=500000000-&BearScore=-&Virtualization=KVM).

## Description

Running `docker-compose up` in a checkout of this repository will start three
containers:

- An Apache/MediaWiki container with PHP 5.6 and MediaWiki 1.25.3
    using [wikimedia/mediawiki](https://hub.docker.com/r/wikimedia/mediawiki/),
    built from https://github.com/gwicke/docker-mediawiki.
- A [MySQL container](https://hub.docker.com/_/mysql/), used as the database
    backend for MediaWiki.
- A
    [wikimedia/mediawiki-node-services](https://hub.docker.com/r/wikimedia/mediawiki-node-services/)
    container built from
    [mediawiki-node-services](https://github.com/gwicke/mediawiki-node-services),
    currently running RESTBase and Parsoid in a single node process for memory
    efficiency.

After startup, a brand new MediaWiki install will be reachable at
http://localhost/.

### Architecture notes

All data is stored outside the containers in a host directory:

```bash
ls /var/lib/mediawiki-docker-compose/
mediawiki-core  mediawiki-mysql  node-services
```

This greatly simplifies backups and upgrades. Update scripts are run on each
startup, which means that updating to a newer version of the entire setup is
as easy as:

```bash
docker-compose pull
docker-compose up
```

Instances can be nuked at any time using `docker-compose rm`, without touching
the data stored in `/var/lib/mediawiki-docker-compose`.

## Status & next steps

This is a fairly early prototype. The basic functionality of MediaWiki +
services is there, but some details about the configuration will likely change
before this can be used in production.

Next steps:

- Hook up VisualEditor out of the box.
- Update to MediaWiki 1.26.
- Forward `/api/rest_v1/` to RESTBase.
- Set up systemd / init scripts to start up the docker-compose setup on boot.
  - Possibly, also provide a systemd-only startup script that doesn't require docker-compose.
- Add more extensions?
- Use HHVM?
- Profit.

Tell us about your ideas at https://phabricator.wikimedia.org/T92826. 
