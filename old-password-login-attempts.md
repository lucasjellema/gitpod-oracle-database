# How to detect newly connected database sessions using the old password

Password Grace Period
As of Oracle Database 19c, we can define a grace period when the password of an account is changed (see for example New Features Guide). During the grace period, sessions can still be created by logging in with the old password. This to allow the new password to be distributed - for example in the form of database wallets - to all applications and servers that need to connect.

Before changing a password with grace period can be done for a particular user, that user needs to be associated with a profile that allows a password grace period

as sys in CDB

show parameter audit_trail 

alter system set audit_trail="db" scope=spfile;

audit session;


/workspace/gitpod-oracle-database/sqlcl/bin/sql sys/"TheSuperSecret1509!"@localhost:1521/xepdb1 as sysdba

create profile password_rollover_time_one_day_profile limit password_rollover_time 1;

alter user APP profile password_rollover_time_one_day_profile;

Let's change the password for user APP - and allow a grace period of one day:

alter user APP identified by newpasswd1

Check if connect with both old and new password

create audit policy connection_policy actions logon WHEN 'SYS_CONTEXT(''USERENV'',''SESSION_USER'')=''APP''' evaluate per session; audit policy connection_policy;

create audit policy connection_policy actions logon WHEN 'SYS_CONTEXT(''USERENV'',''SESSION_USER'')<>''ZZZZAPP''' evaluate per session; audit policy connection_policy;

select to_char(event_timestamp, 'DD/MM/YYYY HH24:MI:SS.FF3'), action_name from unified_audit_trail where dbusername='APP' order by 1;

To drop audit policy:

noaudit policy connection_policy; drop audit policy connection_policy;


SELECT USERNAME, TIMESTAMP, OS_USER, TERMINAL, ACTION_NAME, OBJECT_NAME, RETURNCODE, AUTHENTICATION_TYPE,
       SUBSTR(LOGON_INFO, INSTR(LOGON_INFO, 'VERIFIER=') + 9) AS PASSWORD_VERIFIER
FROM DBA_AUDIT_TRAIL
WHERE ACTION_NAME = 'LOGON';

Resources

ChatGPT

Tim Scott: Gradual Database Password Rollover Time (PASSWORD_ROLLOVER_TIME) in Oracle Database 19c and 21c

SELECT VALUE FROM V$OPTION WHERE PARAMETER = 'Unified Auditing'

Initially returns false. That means that unified auditing is not enabled. I am trying to figure out how to enable it.

select action_name, dbusername, authentication_type, scn from unified_audit_trail where action_name= 'LOGON'

At some point

MyOracle Support - How To Audit Of User Login & Logoff of Database by Unified Auditing

https://alexandruboerescu.ro/2019/06/18/review-failed-login-attempts-on-oracle-xe/