SMF: Simple Machines Forum 1.1.x in Docker
===========================================

Per [SMF1.1:Requirements_and_recommendations](https://wiki.simplemachines.org/smf/SMF1.1:Requirements_and_recommendations), 

> The following are required on any server intending to run SMF 1.1.x.
> - A webserver with approximately 20MB of available disk space. Much more is recommended so that other content can be uploaded over time.
> - A webserver that supports PHP, such as Apache or Internet Information Server (IIS).
> - PHP 4.1.0 or higher.
>   - SMF 1.1 will not work on PHP 5.5 and may not work correctly on PHP 5.4. If you use either of those PHP versions, it is recommended to use SMF 2.0.
> - The following must be changed in the php.ini file:
>   - The engine directive must be set to On.
>   - The magic_quotes_sybase directive must be set to Off.
>   - The session.save_path directive must be set to a valid directory or empty.
>   - The file_uploads directive must be set to On.
>   - The upload_tmp_dir directive must be set to a valid directory or empty.
> - MySQL 4.0.18 or higher.
> - The following are requirements for the database:
>   - For a clean SMF installation, at least 2 MB of storage space in the database. Please note that this is only enough for the installation.
>   - The database user must have at least the following privileges: SELECT, INSERT, UPDATE, DELETE, ALTER, and INDEX.
>   - The database user must have the CREATE and DROP privileges during installation, conversion and some package installs.

Despite the above, I have observed SMF 1.1.x running on a server with PHP 5.4, so let's use that as our target.

Failed to Resolve Source Metadata
---------------------------------

Let's try the official `php:5.4-apache` image:

```dockerfile
FROM php:5.4-apache
```

```
ERROR: failed to build: failed to solve: php:5.4-apache: failed to resolve source metadata for docker.io/library/php:5.4-apache: support Docker Image manifest version 2, schema 1 has been removed. More information at https://docs.docker.com/go/deprecated-image-specs/
```

This image doesn't work with modern versions of Docker, so let's see about rebuilding it with a modern version of Docker ourselves. Head to [docker-library/php](https://github.com/docker-library/php) and... it's gone. With some git forensics, we can find the last commit where it was available was [995bf17](https://github.com/docker-library/php/tree/995bf17c9ded3d622f6b9efb902756562538ab13/5.4/apache). Just copy the files and let it rip, right?

Index Files Failed to Download
------------------------------

```dockerfile
FROM debian:jessie
```

```
ERROR: failed to build: failed to solve: process "/bin/sh -c apt-get update && apt-get install -y ca-certificates curl librecode0 libsqlite3-0 libxml2 --no-install-recommends && rm -r /var/lib/apt/lists/*" did not complete successfully: exit code: 100
```

The `apt-get` commands are failing. `debian:jessie` was EOL'd years ago, of course they're failing! Luckily, there's a set of End of Life Debian versions (pointing at archive.debian.org) available on the Docker Hub at [debian/eol](https://hub.docker.com/r/debian/eol/).

ha.pool.sks-keyservers.net: Host not found
------------------------------------------

Let's update our base image once more.

```dockerfile
FROM debian/eol:jessie
```

```
ERROR: failed to build: failed to solve: process "/bin/sh -c set -xe \t&& for key in $GPG_KEYS; do \t\tgpg --keyserver ha.pool.sks-keyservers.net --recv-keys \"$key\"; \tdone" did not complete successfully: exit code: 2
```

A [StackOverflow post](https://stackoverflow.com/a/68328830) informs you that this server shut down five years ago, but `keyserver.ubuntu.com` should work in its place. And it does!

Next, let's see if there's some prior work getting any version of SMF running in Docker. [vortexau/SMF-Docker](https://github.com/vortexau/SMF-Docker/blob/master/Dockerfile) appears to have been created to run 2.0.x, so let's see what we can learn from it.

gzip: stdin: not in gzip format
-------------------------------

This appears to be the most important bit, let's copy it and add it to our Dockerfile. (the original one used `wget`, our container has curl instead so let's use `curl -O` in `wget`'s place)

```dockerfile
# Download SMF
RUN mkdir -p /var/www/html \
    && cd /var/www/html \
    && curl -O "https://download.simplemachines.org/index.php/smf_2-0-15_install.tar.gz" \
    && tar zxf smf_2-0-15_install.tar.gz
```

```
gzip: stdin: not in gzip format
```

It looks like something went wrong with the download. Try the `curl` command locally and... It's returning a CloudFlare challenge page. Bummer, we'll have to obtain SMF in a web browser where we can solve a CAPTCHA. But, we wanted 1.1.x anyway, so head to the [download link](https://download.simplemachines.org/index.php?thanks;filename=smf_1-1-21_install.tar.gz) and place the file next to our Dockerfile instead.

```dockerfile
# Extract SMF
COPY smf_1-1-21_install.tar.gz /var/www/html/
RUN cd /var/www/html \
	&& tar zxf smf_1-1-21_install.tar.gz
```

All that's left is to add mysql support with `RUN docker-php-ext-install mysql` and add in the chmod block from the vortexau Dockerfile, and we're finally off and running! Browsing to http://localhost/ directs us to the SMF installer.
