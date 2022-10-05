# gitpod-oracle-database
Run Gitpod workspace with Oracle Database

Oracle Database XE is running in a container called *oracle-database-xe*.
You can access the database at port 1521 on localhost - using user SYS (as SYSDBA) and password TheSuperSecret1509! or user developer with password devPassword!

From a bash terminal, you can connect into the database using SQL*Plus with this command:

```
docker exec -it oracle-database-xe sqlplus sys/TheSuperSecret1509! as sysdba
```

The database is started using the Docker Container image prepared by Gerald Venzl. See [Container Image Details](https://github.com/gvenzl/oci-oracle-xe). 

You can use the VS Code Oracle Developer Tools extension for VS Code - which is preconfigured with a database connection for the SYS account.

SQLcl command line tool (see [SQLcl introduction](https://www.oracle.com/database/sqldeveloper/technologies/sqlcl/) for details) has also been installed and can be accessed using:

```
sql sys/"TheSuperSecret1509!"@localhost:1521/xe as sysdba 
```  

A database user has been setup with username DEV and password DEV_PW and another one with APP/APPPW - both in PDB `XEPDB1`. A connection for this user is created in VS Code Oracle Developer Tools. Connect through SQLcl can be done using:

```
sql DEV/DEV_PW@localhost:1521/XEPDB1 
```  
