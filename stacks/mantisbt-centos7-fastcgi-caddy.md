# MantisBT/CentOS7/FastCGI/Caddy

{% embed url="https://medium.com/google-cloud/deploying-mantis-bug-tracker-on-google-cloud-platform-c1810dcc0c71" caption="This ended up written up for use on GCP" %}

## Dependencies

Need `epel-release` package installed.

```text
sudo yum install php php-pgsql php-mbstring php-fpm postgresql-server postgresql
```

## Database Server

In the trial environment I am using postgresql on the VM

```text
sudo postgresql-setup initdb
sudo systemctl enable postgresql
sudo systemctl start postgresql
```

Setup the postgres password

```text
$ sudo passwd postgres
# enter new password
$ su - postgres
$ psql
postgres=# \password 
# enter same password
postgres=# \q
$ exit
```

As the `postgres` user also modify `/var/lib/pgsql/data/pg_hba.conf` to set `md5` method for all IPv4 local connections.

```text
host    all             all             127.0.0.1/32            md5
```

## Mantis Install

Grab the source

```text
curl -O "https://kent.dl.sourceforge.net/project/mantisbt/mantis-stable/2.6.0/mantisbt-2.6.0.tar.gz"
```

Make a `/srv/www` directory and extract the contents into a folder `mantisbt` .

```text
semanage fcontext -a -t httpd_sys_content_t "/srv/www(/.*)?"
restorecon -R -v /srv/www
```

## Caddy Install

Grab the tarball

```text
curl -O caddy.tar.gz "https://caddyserver.com/download/linux/amd64?license=personal"
```

Follow the installation instructions in [caddy/dist/init/linux-systemd at master · mholt/caddy · GitHub](https://github.com/mholt/caddy/tree/master/dist/init/linux-systemd) to enable systemd support, substituting `/var/www` for `/srv/www` .

The `/etc/caddy/Caddyfile` is as simple as:

```text
192.168.124.20:8080
root /srv/www/mantisbt
gzip
fastcgi / 127.0.0.1:9000 php
log stdout
```

Allow web server to connect to database.

```text
sudo setsebool -P httpd_can_network_connect_db on
```

Enable firewall rule

```text
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
```

## Configure PHP-FPM

By default php-fpm is configured to use the `apache` user on CentOS. Caddy and the mantisbt files use `www-data` so update this in `/etc/php-fpm.d/www.conf` by changing the `user` and `group` directives.

