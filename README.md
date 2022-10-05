# gitpod-oracle-database
Run Gitpod workspace with Oracle Database

Oracle Database XE is running in a container called *oracle-database-xe*.
You can access the database at port 1521 on localhost - using user SYS and password TheSuperSecret1509!

From a bash terminal, you can connect into the database using SQL*Plus with this command:

```
docker exec -it oracle-database-xe sqlplus sys/TheSuperSecret1509! as sysdba
```
