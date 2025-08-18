# Build Image

```sh
podman build -t shaderlabs/wiki .
```

# Quadlets

Copy the files in the `quadlets` directory to `~/.config/containers/systemd/`
and run `systemctl --user daemon-reload`.

# Volumes

All folders must be created before running the containers.

- `~/production/wiki/mediawiki/images`
    - Directory where the images are stored.
    - The user/group `www-data` (33) must have write permissions to this directory to enable file uploads.
    This can be achieved by running `podman unshare chown -R 33:33 ~/production/wiki/mediawiki/images`.
- `~/production/wiki/mediawiki/LocalSettings.php`
    - Configuration file.
    - Mounted as read-only.
- `~/production/wiki/mariadb/data`
    - Database data directory.

# Environment Variables

- `~/production/wiki/mariadb/mariadb.env`
    - Environment variables for the MariaDB container.
    -   ```sh
        MYSQL_DATABASE=mediawiki
        MYSQL_USER=mediawiki
        MYSQL_PASSWORD=[PW]
        MYSQL_ROOT_PASSWORD=[PW]
        MARIADB_AUTO_UPGRADE=1
        ```
# Start

```sh
systemctl --user start mariadb
systemctl --user start mediawiki
```

# Various Commands

```sh
# TTY in mediawiki container.
podman exec -it systemd-mediawiki bash

# Run the mediawiki update script.
podman exec -i systemd-mediawiki sh -c 'exec php maintenance/run.php update'

# Import a database dump in `wiki.sql`.
podman exec -i systemd-mariadb sh -c 'exec mariadb -uroot -p[PW] --database=mediawiki' < wiki.sql
```
