# eea.docker.dampos

run the container with docker-compose:

create the data container dampos_home and dampos_data (see below)

```
# git clone <reponame>
# docker-compose build 
# docker-compose up -d
```

run the container: 

```
# docker run --restart=always --name dampos --volumes-from=dampos_home --link damposdb:damposdb -d -p <port_host>:80 eeacms/dampos
```

damposdb:

```
# docker run --restart=always -d --volumes-from=dampos_data --name damposdb postgres:9.3
```

current <port_host> = 50008

moving data volume containers from one host to another:

- donor host

```
# docker run --rm --volumes-from=eeadockerdampos_dampos_1 -v $(pwd):/backup busybox tar zcvf /backup/dampos_home.tar.gz /var/local
# docker run --rm --volumes-from=eeadockerdampos_damposdb_1 -v $(pwd):/backup busybox tar zcvf /backup/dampos_data.tar.gz /var/lib/postgresql/data
```

- target host

```
# docker run -d --name dampos_data eeacms/postgres_data
# docker run -d --name dampos_home eeacms/var_local_data
# docker run --rm --volumes-from=dampos_home -v $(pwd):/backups busybox tar zxvf /backups/dampos_home.tar.gz
# docker run --rm --volumes-from=dampos_data -v $(pwd):/backups busybox tar zxvf /backups/dampos_data.tar.gz
```

## Release to production

The production deployment isn't made with `git clone` and `docker-compose build`.
Instead it pulls an image from Docker Hub. When you have tested your changes
and are satisfied, then you must push a new image up. Here is how you do it:

    docker build -t eeacms/dampos_dem:latest .
    docker push eeacms/dampos_dem:latest

There is also an automated build that creates a new image on Docker Hub when a change is pushed to GitHub.

This mechanism will allow us to later implement release versioning and rollback of deployments.

