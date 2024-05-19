# Oracle Database 19c Redo Log Resizing

![RedoLog ReSizing](https://github.com/MdAhosanHabib/Oracle_RedoLog_Resizing/assets/43145662/a73a54a5-7cac-49e0-a981-51daddc1e1f2)

In this document, we will set our Redo Log size from 50MB to 200 MB in Oracle 19c database.

## We have to know about 6 different states of Redo Log
CURRENT:- Current redo log. This implies that the redo log is active. The redo log could be open or closed.

ACTIVE: The log is active but is not the current log. It is needed for crash recovery. It may be in use for block recovery. It may or may not be archived.

INACTIVE:- Log is no longer needed for instance recovery. It may be in use for media recovery. It might or might not be archived

UNUSED:- Online redo log has never been written to. This is the state of a redo log that was just added, or just after a RESETLOGS, when it is not the current redo log.

CLEARING:- Log is being re-created as an empty log after an ALTER DATABASE CLEAR LOGFILE statement. After the log is cleared, the status changes to UNUSED.

CLEARING_CURRENT:- Current log is being cleared of a closed thread. The log can stay in this status if there is some failure in the switch such as an I/O error writing the new log header.

Dropping of redo log file group which are CURRENT and Active is not allowed where as UNUSED and INACTIVE redo log group can dropped.


## Resize Oracle Database Redo log
### First, check the current status of redo log file:
```bash
sql> select member from v$logfile;
/u02/database/oradata/TEST/redo03.log
/u02/database/oradata/TEST/redo02.log
/u02/database/oradata/TEST/redo01.log

sql> select group#, sequence#, bytes, archived, status, members from v$log;
1	31291	52428800	YES	INACTIVE	1
2	31292	52428800	NO	CURRENT	    1
3	31290	52428800	YES	INACTIVE	1
```

### Then, add desired Redo Logs
```bash
alter database add logfile group 4 '/u02/database/oradata/TEST/redo04.log' size 200m reuse;
alter database add logfile group 5 '/u02/database/oradata/TEST/redo05.log' size 200m reuse;
alter database add logfile group 6 '/u02/database/oradata/TEST/redo06.log' size 200m reuse;
```

### Finally, Delete old Redo Logs by the Group Number

#### First, check the log stat which want to delete.
```bash
sql> select group#, sequence#, bytes, archived, status, members from v$log;
```

#### If, UNUSED or INACTIVE
```bash
sql> alter database drop logfile group 1;
```

#### Else, Current or Active
```bash
sql> alter system switch logfile;

sql> ALTER DATABASE CLEAR UNARCHIVED LOGFILE GROUP 1;

sql> select group#, sequence#, bytes, archived, status, members from v$log;

sql> alter database drop logfile group 1;
```


Then, delete the old one by one.


That's All.

Regards from Ahosan.


Medium:
