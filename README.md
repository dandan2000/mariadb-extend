Extending image
This image can be extended in Openshift using the Source build strategy.

oc new-app mariadb:10.3~https://github.com/dandan2000/mariadb-extend \
    --name my-mariadb-rhel8 \
    --context-dir=mariadb-extend \
    --env MYSQL_OPERATIONS_USER=opuser \
    --env MYSQL_OPERATIONS_PASSWORD=oppass \
    --env MYSQL_DATABASE=opdb \
    --env MYSQL_USER=user \
    --env MYSQL_PASSWORD=pass


The directory passed to Openshift can contain these directories:

mysql-cfg/ When starting the container, files from this directory will be used as a configuration for the mysqld daemon. envsubst command is run on this file to still allow customization of the image using environmental variables

mysql-pre-init/ Shell scripts (*.sh) available in this directory are sourced before mysqld daemon is started.

mysql-init/ Shell scripts (*.sh) available in this directory are sourced when mysqld daemon is started locally. In this phase, use ${mysql_flags} to connect to the locally running daemon, for example mysql $mysql_flags < dump.sql

Variables that can be used in the scripts provided to s2i:

$mysql_flags arguments for the mysql tool that will connect to the locally running mysqld during initialization

$MYSQL_RUNNING_AS_MASTER variable defined when the container is run with run-mysqld-master command

$MYSQL_RUNNING_AS_SLAVE variable defined when the container is run with run-mysqld-slave command

$MYSQL_DATADIR_FIRST_INIT variable defined when the container was initialized from the empty data dir

During the s2i build all provided files are copied into /opt/app-root/src directory into the resulting image. If some configuration files are present in the destination directory, files with the same name are overwritten. Also only one file with the same name can be used for customization and user provided files are preferred over default files in /usr/share/container-scripts/mysql/- so it is possible to overwrite them.


MySQL root user
The root user has no password set by default, only allowing local connections. You can set it by setting the MYSQL_ROOT_PASSWORD environment variable. This will allow you to login to the root account remotely. Local connections will still not require a password.

To disable remote root access, simply unset MYSQL_ROOT_PASSWORD and restart the container.

REMEMBER: to log in with explict root user, otherwise you will log in like pod user (not root) and will not have access to all databases;

mysql -u root

FROM:
https://catalog.redhat.com/software/containers/rhel8/mariadb-103/5ba0acf2d70cc57b0d1d9e78
