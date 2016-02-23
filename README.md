# eea.docker.dampos

run the container with docker-compose:

create the data container dampos_home and dampos_data (see below)

    git clone <reponame>

    docker-compose build 

    docker-compose up -d

run the container: 

    docker run --restart=always --name dampos --volumes-from=dampos_home --link damposdb:damposdb -d -p <port_host>:80 eeacms/dampos

damposdb:

    docker run --restart=always -d --volumes-from=dampos_data --name damposdb postgres:9.3

current <port_host> = 50008

moving data volume containers from one host to another:

<donor host>

    docker run --rm --volumes-from=dampos_data -v $(pwd):/backup busybox tar cvf /backup/dampos_data.tar /var/lib/postgresql/data
    docker run --rm --volumes-from=dampos_home -v $(pwd):/backup busybox tar cvf /backup/dampos_home.tar /var/local

<target host>

    docker run -d --name dampos_data eeacms/postgres_data
    docker run -d --name dampos_home eeacms/var_local_data

    docker run --rm --volumes-from=dampos_home -v $(pwd):/backups busybox tar xvf /backups/dampos_home.tar
    docker run --rm --volumes-from=dampos_data -v $(pwd):/backups busybox tar xvf /backups/dampos_data.tar
