# MariaDB on Wodby

What Wodby and the image set up for this database service. Check it before creating databases or users by hand, or adding connection settings to an application.

## Database, user and passwords

Wodby manages the database and its user; the application does not create them.

- One database and one user are created for the environment, both named after the application and the environment. The user is granted all privileges on that database only.
- The database is created with the charset and collation chosen for it on Wodby (`utf8mb4` by default).
- The user's password and the `root` password are generated once per environment (tokens `password` and `root_password`). The container has the root password in `MYSQL_ROOT_PASSWORD`.
- Further databases and users are added on Wodby, which runs the same create and grant actions. A user created by hand with a name Wodby later needs, but another password, makes the create action fail rather than replace it.

## How a linked service reaches it

- Host: the name of this app service inside the environment. Port: `3306`.
- A service linked to this one receives the host, port, database name, user name and password as environment variables defined by its own link (for example `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USERNAME`, `DB_PASSWORD` on the PHP service). Read those in the application; do not hardcode them and do not use `root` from the application.

## Server configuration

The container writes `/etc/mysql/my.cnf` on every start from environment variables. Change a setting by setting the variable on this service, not by editing the file. The ones used most:

- `MYSQL_INNODB_BUFFER_POOL_SIZE`, `MYSQL_MAX_CONNECTIONS`, `MYSQL_MAX_ALLOWED_PACKET`
- `MYSQL_CHARACTER_SET_SERVER`, `MYSQL_COLLATION_SERVER`
- `MYSQL_WAIT_TIMEOUT`, `MYSQL_INNODB_LOCK_WAIT_TIMEOUT`, `MYSQL_TRANSACTION_ISOLATION`
- `MYSQL_SLOW_QUERY_LOG`, `MYSQL_LONG_QUERY_TIME`, `MYSQL_GENERAL_LOG`

A change applies with the next deployment of the service. Host names are not resolved (`skip-name-resolve`), and the client character set handshake is skipped: connections use the server character set.

## Data, backups and imports

- Data is on the `data` volume, mounted at `/var/lib/mysql`.
- The backup is a gzipped SQL dump of the environment's database made with `mariadb-dump --single-transaction`. Tables listed in its "excluded table contents" option (names or `LIKE` patterns) keep their definition and lose their rows.
- The database import replaces the data volume: the dump is loaded while a new, empty data directory is initialized. It accepts `.gz`, `.tar.gz`, `.tgz` and `.zip` holding exactly one `.sql` or `.mysql` file.
- After a version upgrade Wodby runs `mariadb-upgrade` and `mariadb-check` on the environment's database and on the `mysql` system database.

## Check the result

In the database container:

- `mariadb -uroot -p"$MYSQL_ROOT_PASSWORD" -e 'SHOW DATABASES;'` lists the databases.
- `mariadb -uroot -p"$MYSQL_ROOT_PASSWORD" -e "SHOW VARIABLES LIKE 'max_connections';"` shows a setting as the server runs it.
