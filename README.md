# gitpod-oracle-database
Run Gitpod workspace with Oracle Database


[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/lucasjellema/gitpod-oracle-database)


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

# Password Grace Period

As of Oracle Database 19c, we can define a grace period when the password of an account is changed (see for example [New Features Guide](https://docs.oracle.com/en/database/oracle/oracle-database/19/newft/gradual-database-password-rollover-applications.html)). During the grace period, sessions can still be created by logging in with the old password. This to allow the new password to be distributed - for example in the form of database wallets - to all applications and servers that need to connect. 

Before changing a password with grace period can be done for a particular user, that user needs to be associated with a profile that allows a password grace period  

/workspace/gitpod-oracle-database/sqlcl/bin/sql sys/"TheSuperSecret1509!"@localhost:1521/xepdb1 as sysdba

create profile password_rollover_time_one_day_profile limit password_rollover_time 1;

alter user APP profile password_rollover_time_one_day_profile;

Let's change the password for user APP - and allow a grace period of one day:

alter user APP identified by newpasswd1 

Check if connect with both old and new password



create audit policy connection_policy actions logon  WHEN 'SYS_CONTEXT(''USERENV'',''SESSION_USER'')=''APP''' evaluate per session;
audit policy connection_policy;

create audit policy connection_policy actions logon  WHEN 'SYS_CONTEXT(''USERENV'',''SESSION_USER'')<>''ZZZZAPP''' evaluate per session;
audit policy connection_policy;

select to_char(event_timestamp, 'DD/MM/YYYY HH24:MI:SS.FF3'), action_name
from unified_audit_trail where dbusername='APP' order by 1;


To drop audit policy:

noaudit policy connection_policy;
drop audit policy connection_policy;



## Resources
Tim Scott: [Gradual Database Password Rollover Time (PASSWORD_ROLLOVER_TIME) in Oracle Database 19c and 21c](https://oracle-base.com/articles/21c/gradual-database-password-rollover-time-21c)

SELECT VALUE FROM V$OPTION WHERE PARAMETER = 'Unified Auditing'

Initially returns false. That means that unified auditing is not enabled. I am trying to figure out how to enable it.

select action_name, dbusername, authentication_type, scn
from unified_audit_trail
where action_name= 'LOGON' 

At some point 


[MyOracle Support - How To Audit Of User Login & Logoff of Database by Unified Auditing](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=179570306193450&parent=EXTERNAL_SEARCH&sourceId=HOWTO&id=2509211.1&_afrWindowMode=0&_adf.ctrl-state=r8fsqq1di_4)