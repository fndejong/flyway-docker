# Flyway Docker images for OpenShiftv3

[![Docker Auto Build](https://img.shields.io/docker/automated/javafactory/openshift-centos7-flyway.svg?style=flat-square)][docker]

This is a fork of the official Flyway repository, which can be found [here](https://hub.docker.com/r/boxfuse/flyway/). Please refer to this repository for complete documentation.

This image uses a non-root user so it can run within an OpenShift cluster.

Dockerfile was inspired (shamelessly copied) by:
* the official Flyway [Dockerfile](https://github.com/flyway/flyway-docker/blob/master/Dockerfile)
* the official WildFly S2i OpenShift [Dockerfile](https://github.com/openshift-s2i/s2i-wildfly/blob/master/11.0/Dockerfile)

## Supported Tags

The following tags are supported:

-	[`5.0.7`, `5.0`, `5`, `latest` (*Dockerfile*)](https://github.com/flyway/flyway-docker/blob/master/Dockerfile)

## Supported Volumes

To make it easy to run Flyway the way you want to, the following volumes are supported: 

Volume | Usage
-------|------
`/flyway/conf` | Directory containing a `flyway.conf` [configuration file](https://flywaydb.org/documentation/commandline/#configuration) 
`/flyway/drivers` | Directory containing the [JDBC driver for your database](https://flywaydb.org/documentation/commandline/#jdbc-drivers) 
`/flyway/sql` | The SQL files that you want Flyway to use (for [SQL-based migrations](https://flywaydb.org/documentation/migration/sql)) 
`/flyway/jars` | The jars files that you want Flyway to use (for [Java-based migrations](https://flywaydb.org/documentation/migration/java)) 

## Getting started

The easiest way to get started is simply to test the image by running

`docker run --rm javafactory/openshift-centos7-flyway`

This will give you Flyway Command-line's usage instructions.

To do anything useful however, you must pass the arguments that you need to the image. For example:

`docker run --rm javafactory/openshift-centos7-flyway -url=jdbc:h2:mem:test -user=sa info`

## Adding SQL files

To add your own SQL files, place them in a directory and mount it as the `flyway/sql` volume.

### Example

Create a new directory and add a file named `V1__Initial.sql` with following contents:

```
CREATE TABLE MyTable (
    MyColumn VARCHAR(100) NOT NULL
);

```

Now run the image with the volume mapped:
                                                             
`docker run --rm -v /my/sqldir:/flyway/sql javafactory/openshift-centos7-flyway -url=jdbc:h2:mem:test -user=sa migrate`

## Adding a config file

If you prefer to store those arguments in a config file you can also do so using the `flyway/conf` volume.

### Example

Create a file named `flyway.conf` with the following contents:

```
flyway.url=jdbc:h2:mem:test
flyway.user=sa
```

Now run the image with that volume mapped as well:
            
`docker run --rm -v /my/sqldir:/flyway/sql -v /my/confdir:/flyway/conf javafactory/openshift-centos7-flyway migrate`

## Adding a JDBC driver

Flyway ships by default with drivers for 

- SQL Server
- MySQL
- MariaDB
- PostgreSQL
- Sybase ASE
- H2
- HSQLDB
- Derby
- SQLite

If your database is not in this list, or if you want to ship a different or newer driver than the one included you
can do so using the `flyway/drivers` volume.

## Adding Java-based migrations and callbacks

To pass in Java-based migrations and callbacks you can use the `flyway/jars` volume. 

### Example

Create a directory and drop for a jar with your Java-based migrations in there.

You can now let Flyway make use of it my mapping that volume as well:
            
`docker run --rm -v /my/sqldir:/flyway/sql -v /my/confdir:/flyway/conf -v /my/jardir:/flyway/jars javafactory/openshift-centos7-flyway migrate`

## OpenShift DeploymentConfig

This image can be used as an [init container](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) to run a migration before a Pod starts.

### Example

TBD.
