#Database in SUSPECT state , what now?

##Know the probable causes and solutions for this problem.

One of the worst situations that a Database Administrator can face is to hear complaints from all users of the system reporting that they cannot access the database or that the application has access errors in the database . After a brief check of this situation , the DBA discovers that one of the production databases is inaccessible and has entered the SUSPECT state .

To exemplify how to work in this situation, we will use two practical cases . A corrupted log file and how to put the bank ONLINE without using the backup restore feature , assuming that this feature is not available . With oo nother case , we will work with the hypothesis of the log file has been accidentally deleted while the SQL Server service is ava down or even in the case of the log file to be corrupted irretrievably , and will require an action without counting that file . To carry out these examples and guide the theory, the version of SQL Server used here must be 2005 or Upper or .

In SQL Server , we have “ database states ” that are representations of how the database is found for process executions. The states define whether the bank is able to work on it or if there is a problem that needs direct action by the DBA . These states are shown below :

ONLINE: The database is available for access. Here they can access and record information;
OFFLINE: The database is unavailable. The SQL Server service does not automatically put the database in this state. It is a state that only the database administrator places it via the ALTER DATABASE [ database ] OFFLINE command ;
RESTORING: The database is unavailable. One or more files group files that make up the est database will be restored by action restore ;
RECOVERING: The database is being recovered. It will be available once the recovery is complete. If not consi g to recover (put the database in ONLINE) the same enters state SUSPECT and becomes inaccessible. The “RECOVERING” state happens every time the SQL Server services are started to test that all objects and their data files are healthy ;
RECOVERY_PENDING: The database is will to some error recovery and further action is required from the user to resolve the error and allow the recovery process to complete. The bank is inaccessible as long as this error is not remedied;
SUSPECT: Some ( n s ) of the database group files is ( will ) corrupted (s) . When SQL Server starts , it checks that the files are healthy . If he finds a problem in these files he puts the database in the “SUSPECT” state . This state n EED direct action of the Administrator B ank D ata and is the state that Job haremos this article;
EMERGENCY: In this state the bank is in single user mode and marked as READ_ONLY in addition to the bank's log recording file is disabled. It is in this state that we will work to resolve the state of the SUSPECT database and to make it ONLINE .
 

The database in the “ONLINE” state is accessible and ready to be connected , with all data recording (such as update , insert or delete ) going through the log file and then going to the datafile file .

In this article we will work with databases in the SUSPECT state . The SUSPECT state means that there is a transactional and / or structural inconsistency. This is the result of a failed recovery process (typical when data page corruption occurs) or the SQL Server service started, but failed to RECOVER the database (typical when an error occurred in the database log or also corruption on page s data).

Because of this attempt to put the database in the air without success, SQL Server n will can ensure the consistency of data and the same brand as SUSPECT . In addition, the bank data will be found as if off line (inaccessible), and the only action possible in this database is going to the state " EMERGENCY " (read - only ) .

 Reasons to corrupt a database

Most problems I / O occurs failures of subsystems such as , for example, failure at some point in the operating system or drivers storage with problems firmware . In such cases, the time d to write the s given s memory RAM (or file SWAP ) for the record, it can occur r a fault where a page, a heading or even even the entire file of the database can be corrupted . Even a failure sudden power when recording also can corrupt the database data .

When we lowered the SQL Server Service to abruptly (with the SHUTDOWN command WITH NOWAIT ), the SQL Server service can record incomplete information in storage and also can cause corruption, because it does not expect the actual recording of data on disk .

Another important detail that can corrupt the database is to leave the AUTO CLOSE option with the value t rue . This causes , so close the last session within the database, the physical files that make up m the database (* . Mdf and *. Ldf ) are free for other operating system services use ,.

With the AUTO CLOSE option set to true , compressing Windows files or even moving one or all of the database's datafiles is entirely possible. Even the antivirus scan itself can block the use of datafiles in SQL Server and not leave the database ONLINE .

That is why it is important to create a rule in the antivirus application so as not to check the SQL Server datafiles folder . This prevents the antivirus from attempting to access these files under any circumstances and also frees any competition between the SQL Server service and the antivirus .

Another situation that may end up corrupting one datafile of database is d eixar "option Page Verify " in NONE or TORN_PAGE_DETECTION . The NONE option does not use the verification of data recording on the disk and the other option ( TORN_PAGE_DETECTION ) is already outdated in versions SQL Server 2005 or greater (since it uses only 2 bytes of verification in the header of the data page). Always leave it in CHECKSUM , because it uses a hash in the header of the data page, much more secure and accurate than the TORN_PAGE_DETECTION method . With SQL Server using CHECKSUM to guarantee data entry on disk, it will not encounter an error when trying to recover the data. Otherwise ( NONE or TORN_PAGE_DETECTION in some moments) it makes the recording without the precise confirmation of the data, causing errors to be issued (as in a select ) and data will be lost. It is good to remember that there is no performance degradation in leaving the “ Page Verify ” option in CHECKSUM .

Upgrade SQL Server versions can corrupt datafiles (yes, there may be corruption of data - files of databases!). So always back up your databases before performing a version upgrade ( cumulative patches also follow the same rule).

Up disk defragmentation can cause corruption of data - files , c ccording to the article Microsoft (A heavily fragmented file in an NTFS volume may not grow beyond a Certain size ) in the links section . This article indicates that for very large files I / O failure and corruption may occur. 

Another reason to put the database in “SUSPECT” state is modifications in the powers of the account that starts the SQL Server service . These modifications can m make the datafiles of the SQL Server file inaccessible, resulting in, apparently, database corrupted . However, in this case, just fix the access and restart the SQL Server service, which returns everything to normal, without having to apply any correction method.

Corruption error messages

. Here are some error messages that SQL Server provides when data are presenting corrupt behavior and necessarily need to check what the cause of this problem s alerts :

 

• Msg 823 (error message) in SQL Server;             

• Msg 825 and 824 (read retry) in SQL Server;             

• SQL Server Page Level Corruption;             

• SQL Server Table Corruption Error;             

• Corruption error in Metadata (data from SQL Server system tables);             

• Errors reported by the DBCC CHECKB command;             

• Corruption in non- clustered indexes .             

 

Above we find the ome messages corruption errors, but there are thousands of othe the s and c ada one to the main recipe is and stay calm , checking what is the source of the problem in the database !

Main features of a corrupted bank

Whenever you hear of any of these symptoms below, be alert, as your database may be showing corrupt database behavior . For this reason, it is always important, in the daily checklist , to go through all the logs that the Windows and SQL Server servers provide:

• Users or application reporting I / O errors 823 and 824 (hardware and software, respectively);             

• SQL Server alerts agent warning:             

• Alerts SQL Server with code s severities greater errors or equal 19;             

• Alerts SQL Server with code 's severities with errors of type 825 (Problem I / O momentary);             

• Failure in backup plans, presenting error 3043 (backup detected “ CHECKSUM ERRORS ”);             

• SQL Server L og s with recurring I / O failure errors , like this:             

“ SQL Server has encountered X occurrence (s) of I / O requests taking longer than XX seconds to complete on file [PATH] in database [Bank] (XX).  The OS file handle is 0x0000000000000XXX. The offset of the latest long I / O is: 0x00000XXXaX0000 ”.

This error is about storage failure where the datafiles are located .

• Application / user connection fails, having the user try to connect to the system again ;             

• The Windows event viewer reports errors related to I / O in the operating system.             

Steps to start seeing a bank suspected of corruption

So. There is a database that has some of the above characteristics or another that indicates an I / O problem . What to do ? First entr not air in panic. At these times it is important to remain calm and work focused on solving the problem.

Determine m the extent of corruption by running the command DBCC CHECKDB in the bank data with the possible corruption of data. The command mentioned will check r physical and logical integrity of the objects found inside the database , sweeping the whole fault searching the database . This verification works at six levels , namely :

E structure of allocation of disk space of the database;
I ntegridade of all pages and structures of database tables;
C onsistência the catalog database data;
C ontent indexed database;
V metadata inputs of SQL Server system tables;
V ALIDATION of Service Broker database.
 

It is worth mentioning that tables that have enabled memory optimization ( feature available since version 2014 ) , the mentioned command will not perform the repair, only the verification of possible errors .

 

Note : Always wait for the DBCC CHECKDB command to run, otherwise it will actually corrupt the database. Keep in mind that the bigger the bank, the longer it will take to finish the command.

 

• Look alerts and SQL Server log and see if the problem still persists determining the how long it is already happening. Some read and write failures are automatically corrected by the operating system, causing SQL Server to save the new information elsewhere. In addition, your database may have been corrupted for so long that the backups that exist and also have the corrupted databases stored in the backups . The backup is intact and it is possible to perform the restore , but the data contained in that backup is corrupted;             

• If there are Jobs to do backups, v erify m if they are in error. It is important to note that backup jobs have alerts to warn if there is an error in execution . Check m is where the backups are stored has enough space for the time window scheduled by the organization;             

• See m that backups are available and make m test s to restore s regularly before having the "surprise" of the database present the state suspect . The most important and safest method is to restore the last valid database backup;             

• Do m backup s from logs of bank data that has to ption of Recovery model in " FULL ". With the log backups up to date it is possible to restore the database at any time in time ( POINT IN TIME ) and also make tests on these files. Many companies fail to pass this test and if any of the log backup files are will corrupt compromises the entire chain of restore , forcing restore only the backup point FULL .             

 

And c interpret atom , to execute the command DBCC CHECKDB on the server, fatal errors that need d to apply a restore of backup in the database or database from the reconstruction of the structure?

Previous to any action, s and the command DBCC CHECKDB Flip Flops air before finishing, a problem very serious must be happening in the bank. This means that there is no option but to trigger a restore and return a valid backup.

The ome code 's fatal error when running the command DBCC CHECKDB . These codes represent a serious problem with your database and need immediate action by the administrator, checking in which table or structure the error occurred :

7984 to 7988 : Critical corruption in the system tables;
8967 : Invalid state of CHECKDB itself ;
8930 : Corrupted metadata . CHECKDB is unable to correct this type of error as it is a corruption of SQL Server metadata . Metadata , in this context, is the SQL Server system catalog ;
 

The errors below are also appointed by the command DBCC CHECKDB and it can not solve (but possibly has a solution using othe the s tools or other solutions that SQL Server provides ):

 

8909, 8938, 8939 : ( C abeçalho corrupted data page);
8970 : Invalid data for the column type . Some type of data was found as a special character (Ex. ' Ð ') in a column that does not accept that type of data ;
8992 error : CHECKCATALOG (incompatibility of metadata ) . This problem occurs because SQL Server does not support manual updates to system tables. System tables should only be updated by the SQL Server database engine . ;
8904 : Size for allocating two objects. For some unexpected disk failure, SQL Server writes the space required for one object as being for two objects, taking up unnecessary space and making data difficult to read.
Tips for analyzing a database

. We will now take a look at some tips and tricks to check what action we should take when a database has corrupt behavior. We will know if it is faster (and less laborious) to use a database restore or try to recover the data using the DBCC CHECKDB command:

Do you still have the online database ? If not, try searching for the solution from a backup. Remember that recovery using backups is not the best way to avoid downtime as it will depend on the backups being accessible at the time and the size of the databases involved for the restore , but it is the best way to recover most (or all) of the data; Remember : Back up the s their base s data and always test your backups in another environment p ara have no surprises in the future;
Did the DBCC CHECKDB command fail? If so, you must seek recovery in a restore , necessarily;
If the error occurred on non- clustered indexes , (corrupted index) , then you may be able to use the ALTER INDEX ... REBUILD (never ONLINE ) option to correct the problem. There will be performance degradation at the time of recreating the index.
Do you have backups working? If not , try to restore with CONTINUE_AFTER_ERROR , or extract the data to a new database. After that, copy it later to the production database.
When the log of the database is c orrompido , you can restore a log backup or run the state of emergency recovery, which will be applied in the examples we will see later.
 

How to repair a database ?

After checking the database we decided to repair it with possible data loss. In this case, the database will be available for access after correction, but we will have data that cannot be saved. Below we will see how we can carry out this repair:

USER walk the command DBCC CHECKDB ([bank ], REPAIR_ALLOW_DATA_LOSS ) with the parameter REPAIR_ALLOW_DATA_LOSS repairs fixed structures of data pages, relocating the data to another area valid . In addition, and l can also perform the reconstruction of the indexes. Repair did not take ha account (correct or not restructure) fo reign keys constraints , l ógicas inherent s business and data rate, r eplicações SQL Server.

Before executing the command to repair, protect yourself by making a backup of the database in its current state and stop replication, if it exists . D fter perform recovery , chequem the data again re-running the command DBCC CHECKDB (without parameters) and the Tiv in again any replication involved (if any) plus r ealiz air a new backup of the database .

 

Database log is damaged

While the system was operating, for some reason the production database had one of the corruption problems mentioned above. We took a look at the state of the database and it is corrupted . We will now see what can be done.

First, check m which type of error is occurring in the log backup. Enjoy also ev erify m SQL Server logs and qualify the error (read that type of error SQL Server is presenting) . Try m retrieve the log from a newly made log backup , because this will always be the best choice.

Put m again the bank in the state ONLINE and work m in correcting the problem of origin ( if it is storage , driver , service pack , etc. ) and do not forget to schedule a maintenance window to fix this problem and get m attentive s to SQL Server error warnings.

When checking to return a backup, it was found that there is no backup available. In this case, we have two realities . The first is u s ing the state database EMERGENCY you can access the b ank data and work on it. To do this, use the command ALTER DATABASE [bank] SET EMERGENCY . In addition, the database must be in SINGLE_USER mode ;

In the EMERGENCY state we can extract the data and write to another non-corrupted database , reconstructing the database manually . This reality is the most difficult.

The second is to use the DBCC CHECKDB command with the REPAIR_ALLOW_DATA_LOSS option to reconstruct the log file (if possible). Remembering that in this case we will possibly have data loss.

 

What not to do . What are the worst practices ?

Below are some examples of actions that we normally take in a critical environment that can create even more problems, instead of solving them. These items cover practices that are very common for unsuspecting administrators:

- Restart the SQL Server service. When we restart the SQL Server service it tries to put all banks in the “ONLINE” state and checks each state of the databases ' datafiles . It will only put the bank in a “ SUSPECT ” state and will not solve the problem . Reset SQL service becomes a waste of time, and takes r other banks of data from the same body of air;

- Skip the steps to check the type of error and apply a database restore without actually checking the need (trying to recover the data or reconstruct the logs );

- Detach (“ d etach ”) a bank in the “ SUSPECT ” state . It will surely fail when you attach again. This is the worst situation and should be avoided as much as possible! In this case, the log file will surely lose data and all transactions that were in it will be lost . There is a possibility to solve this case using a hack method (“ jaqued-up ”) which will be shown later in the article .

 

 

[CHECKPOINT]

Example 1 - Database with corrupted log

First, we created a database called BaseComLogSuspito that will be used to create our model structure and also to insert the data and then, purposely create the corruption in the database . This bank will be created in the default SQL Server mode without any custom parameters. Use m the SQL Server Management Studio to perform the steps and l istagem s to follow . Within the BaseComLogSuspeito bank , we will create a table with information from student grades.

 

Note : It is important to remember that, to perform this example, we will reboot the SQL Server service several times. P ortanto , use m an environment which has no impact on performing this process . In this example , we will create a table (with the name NotaDosAlunos ) not following the rules of Normal Forms , that is, without respecting the normalization of data and its 5 normal forms . It will only serve the purpose stated.

 

Listing 1 . Creating the bank and populating

USE [master]

GO

 

CREATE DATABASE [BaseComLogSuspeito]

GO

 

USE [BaseComLogSuspito]

GO

 

CREATE TABLE [StudentNotes]

              (

    [StudentName] VARCHAR ( 50 ),             

    [Gang] VARCHAR ( 30 ),                                         

    [Note] float                                         

    );

GO

 

INSERT INTO [StudentNotes]  VALUES ( 'Eduardo' , 'A' , 7.8 );

INSERT INTO [StudentNotes]  VALUES ( 'João' , 'B' , 10 );

GO

 

We will check whether the data was successfully written to the table through it the Listing 2 and the result in Figure 1 . These data will be used for the purpose of recording the information in the log file and, afterwards, corrupting them in order to be able to retrieve it later :

 

Listing 2 . Checking if the data was successfully saved

SELECT [StudentName] ,             

                            [Gang] ,

                            [Note]

FROM [StudentNotes]

Figure 1 . Result of data inclusion

 

We recorded two successful records in the table NotaDosAlunos successfully . Now we are going to accidentally update Eduardo's note by placing the value zero.  Thus, we will force the recording of the update disk with the command CHECKPOINT , as Listing 3 , ensuring the writing of information in the s files log .

 

Listing 3 . Performing a transaction by forcing its recording with CHECKPOINT

BEGIN TRAN ;

UPDATE

    [StudentNotes]

SET

    [Note] = 0

ONDE

    [StudentName] = 'Eduardo' ;

GO

 

CHECKPOINT

GO

 

I STOPPED HERE In another window, we will simulate a power outage, downloading the SQL Server service abruptly with the WITH NOWAIT parameter (Listing 4 ) . The NOWAIT d parameter turns on the SQL Server service without running checkpoints on the entire database :

 

Listing 4 . Stop the SQL Server service abruptly

SHUTDOWN WITH NOWAIT

GO

 

With the SQL Server service stopped, we will simulate a physical damage in the log file , using the HxD software ( session links) which is a FREE memory editor and Hexadecimal file.

To this, p rimeiramente urges le software (type next , ne x t , finish ). After installation, and run the software and in the file menu , option open and go to where the log file is . Press it and will display a screen like the Figure 2 .

 



Figure 2. Hexadecimal Editor screen

 

In the first line where 00000000 is located (below Offset (h)) fill the line with zeros (“0”) until the end of it, to simulate a log file recording error and leave it corrupted, as shown in Figure 3 :

 



Figure 3. Line to edit and simulate a log file corruption error

 

Save the file in the same folder as the original file (replacing the file) . Note that the software itself already creates a backup file, called BaseComLogSuspeito_log.LDF.bak without having to interact and create a backup file . If any problem occurs and you cannot simulate the error, delete the file edited by the HxD software and rename the backup to its original name.

Now we are going to put the SQL Server service back online. In the Command Prompt window of the server (cmd.exe), we execute the command: NET START MSSQLSERVER (assuming the SQL Server instance is the default). The companion in Figure 4 .

 



Figure 4 . Starting the SQL Server service

 

With the SQL Server service on the air, we tried to enter the database ( Listing 5 ) , where it will present the error in Figure 5 , warning that the BaseComLogSuspe database does not have its files accessible or there is not enough memory on disk .

 

Listing 5 . Accessing the database

USE [BaseComLogSuspito]

GO

 

We are faced with the following message ( Figure 5 ):



Figure 5 . Error screen due to the log being corrupted

 

Through Listing 6 , we verify the state of the database. In this case, the database will be in “ SUSPECT ” state, making it impossible to carry out transactions on it. In this state, the database only allows changing its state to “EMERGENCY , a state in which data can only be read in the database.

 

Listing 6 . Checking the state of the database

SELECT DATABASEPROPERTYEX ( N'BaseComLogSuspeito ' , N'STATUS' ) AS N'Status' ;

GO

 

And it is in “ SUSPECT ” state , as shown in the screen below , in Figure 6 :



Figure 6 . Database status in “ SUSPECT ”

 

Our bank is already in the state we wanted to be able to run the laboratory. So the first step is to place the bank in a state " EMERGENCY " (read only) and so SINGLE_USER (only one user accessing the database) , confome Listing 7 , for s nly so, run the command DBCC CHECKDB . It is important that if you do not put the database in SINGLE_USER mode, it will not let you perform any repairs on the database .

 

Listing 7 . Correcting the state of the database

ALTER DATABASE [BaseComLogSuspeito] SET EMERGENCY ;

GO

ALTER DATABASE [BaseComLogSuspeito] SET SINGLE_USER ;

GO

 

DBCC CHECKDB ( N'BaseComLogSuspeito ' , REPAIR_ALLOW_DATA_LOSS ) WITH NO_INFOMSGS , ALL_ERRORMSGS

GO

 

We run the DBCC CHECKDB command with the options NO_INFOMSGS (do not display informational messages) and ALL_ERRORMSGS (display all error messages). The REPAIR_ALLOW_DATA_LOSS option , in this case, retrieves the log file and deletes the pages where the corruption is, recreating the same empty ones . The result is shown in Figure 7 .

 

 



Figure 7 . Recreated log base

 

What the DBCC CHECDB command did was to rebuild the log file ( The log for database ' BaseComLogSuspeito ' has been rebuilt ). But were the data saved from that UPDATE that was performed?

We will try again to enter the bank and see how the table [ NotaDosAlunos ] looks like . But first it is necessary to put the bank “ ONLINE ” again and in multi- user mode , according to Listing 8 . The result of executing these commands is shown in Figure 8 .

 

Listing 8 . Putting the database back “ ONLINE ” and checking the data

ALTER DATABASE [BaseComLogSuspeito] SET ONLINE

GO

ALTER DATABASE [BaseComLogSuspeito] SET MULTI_USER

GO

USE [BaseComLogSuspito]

GO

 

SELECT [StudentName] ,             

                            [Gang] ,

                            [Note]

FROM [StudentNotes]

GO



 

Figure 8 . Recreated log base

 

Note m he did not record the information, because the command had not yet finished with COMMIT (disk recording in the data file) , but only recorded the information in the log file ( CHECKPOINT ).

What if this command was not accidental?  Then we would have data loss, which would be expected for the execution of this command. Its advantage is that, depending on the size of the database and the restore process (searching for the restore file , restoring it and putting the database online again) it takes longer and it is more worth losing some data and entering the same data manually after the bank is “ ONLINE ”.

 

Example 2 - Database with deleted log

 

As in Example 1, we will create the base and create the table with the data ( Listing 9 ) . In this new example, we will untack the database and delete the log file from the database, creating a situation where it is impossible to recover the log file (assuming that the log file is not backed up) . In Listing 10 we run the update to simulate a transaction and, in another window, we execute the command in Listing 11 , shutting down the SQL Server service normally :

 

Listing 9 . Creating the database and populating it

USE [master]

GO

 

CREATE DATABASE [BaseSemLog]

GO

 

USE [BaseSemLog]

GO

 

CREATE TABLE [StudentNotes]

              (

    [StudentName] VARCHAR ( 50 ),             

    [Gang] VARCHAR ( 30 ),                                         

    [Note] float                                         

    );

GO

 

INSERT INTO [StudentNotes]  VALUES ( 'Eduardo' , 'A' , 7.8 );

INSERT INTO [StudentNotes]  VALUES ( 'João' , 'B' , 10 );

GO

 

Listing 10 . Creating the database and populating it

BEGIN TRAN ;

UPDATE

    [StudentNotes]

SET

    [Note] = 0

ONDE

    [StudentName] = 'Eduardo' ;

GO

 

COMMIT

GO

 

Listing 1 1 . Normally downloading the SQL Server service

SHUTDOWN

GO

 

We go to the folder where the SQL Server datafiles are located and delete the BaseSemLog .ldf file ( database log file ). After that, we will put the SQL Server service back online. In the Command Prompt window of the server ( cmd.exe ), we execute the command: NET START MSSQLSERVER (assuming the SQL Server instance is the default ) .

With the SQL Server service in the air, try to enter the bank ( Listing 12 ) , noting that again we have the error ( Figure 9 ) similar to that of Example 1 and look the s State Bank , verifying that it this in way " SUSPECT ”( Listing 13 and Figure 10 ) .

 

Listing 1 2 . Entering the database

 

USE [BaseSemLog]

GO

 

Again we have the error similar to example 1 ( Figure 9 ):


 

Listing 1 3 . Entering the database

SELECT DATABASEPROPERTYEX ( N ' BaseSemLog ' , N'STATUS ' ) AS N'Status' ;

GO

 



Figure 10 . “ SUSPECT ” basis

 

At this point, instead of trying to recover the database with the command DBCC CHECDB , let's try desatachar ( des attach ) the bank data ( Listing 1 4 ) , trying to put it soon after again on the server. In SQL Server 2008 is shown the error of Figure 1 1 leaves ing detach the database , since the SQL Server 2005 is possible, presenting the message of Figure 1 2 .

 

Listing 1 4 . Trying to challenge Nexar the base 'BaseSemLog'

EXEC sp_detach_db 'BaseSemLog' ;

GO

 



Figure 1 1 . Error detaching 'BaseSemLog' in SQL Server 2008

 



Figure 1 2 . Detach message in SQL Server 2005

 

In the version of SQL Server 2005, he managed to detach the database, but he said he was unable to successfully shut down the database on the server. In this case, q hen happens " detach " in version 2005 , we can still put the database in the air through d to Listing 1 5 , recreating the zero database log file ( Figure 1 3 ).

 

Listing 1 5 . Attacking the base again without the log file in SQL Server 2005

EXEC sp_attach_single_file_db   @dbname = 'BaseSemLog' ,

@physname = N 'E: \ SQL2005 \ DATA \ BaseSemLog.mdf' ;

GO

 



Figure 1 3 . Base attacked with recreated log file .

 

To solve the overall problem of loss of the log file in SQL Server 2008 (different from the 2005 version) let u s ing the command sp_resetstatus that disables the database state " SUSPECT ", updating the metadata of the sys. databases from the master database . To illustrate this, ex ecuta re hands the control of Listing 1 6 and will be displayed message ( Figure 1 4 ) :

 

Listing 1 6 . Resetting the database status

EXEC sp_resetstatus 'BaseSemLog'

GO

 



Figure 1 4 . Reset of 'BaseSemLog' base status

 

After this command is possible to restructure the basis of log, as in Example 1 , with the commands d the Listing 1 7 , which will be fully restructured the log file, but no information CHECKPOINT . The result is shown in F igure 1 5 , where it shows the message that you must run a new DBCC CHECKDB to check the consistency of the data. After that, it is necessary to grant access to the multi-user option with the command below:
 

ALTER DATABASE BaseSemLog SET MULTI_USER

 

Listing 1 7 . Re and struturar log base

ALTER DATABASE BaseSemLog SET EMERGENCY

ALTER DATABASE BaseSemLog SET SINGLE_USER WITH ROLLBACK IMMEDIATE

DBCC CHECKDB ( 'BaseSemLog' , REPAIR_ALLOW_DATA_LOSS ) WITH NO_INFOMSGS  

 



Figure 1 5 . Recreated 'BaseSemLog' database log file.

 

Note m that , in all those cases, the log file has been rebuilt and can not save the information you had in it. In addition, there is the possibility of some inconsistency (again) in the database. So, run a DBCC CHECKDB immediately to see the “health” of the database.

With this action, too much information can be lost, INFORMATION s which is still vam within the log file and was not transferid to for the data file (* . Mdf ). This will also affect the performance of SQL Server, as by recreating the log file, it will be at its minimum size. And SQL Server uses the log file to work on the recovery of the information in the database, and to revamp, relocate and open new physical space in s torage . Many problems related to deadlock are due to this. When recreating the log file or when executing a Shrinkfile on the log file with the option TRUNCATEONLY .

 

Box 1 . Freeing up space with the TRUNCATEONLY option

 

Many indicate running the commands below in order to free up disk space:

 

ALTER DATABASE Databases SET RECOVERY SIMPLE WITH NO_WAIT

GO

DBCC SHRINKFILE ( N'BancodeDados' , 0, TRUNCATEONLY)

GO

ALTER DATABASE Databases SET RECOVERY FULL WITH NO_WAIT

GO

 

However, this case leaves the log file at its minimum size, forcing the information to be written to the data file, besides, all query optimization (used in cache) is lost and SQL Server is forced to restart its architectures. to optimize queries from scratch. Just like the case of losing the log file and having to recri will it does (as in example 2).

Therefore, make backups of the log files and leave it to the SQL Server itself to manage the free space of the log file .

Check with the DBCC OPENTRAN command which transactions are still active in the database and also check what they are doing, with the DBCC INPUTBUFFER ( SPID) command , where SPID is the session found in the DBCC OPENTRAN command .

It is better to bring down a transaction only than to lose data or cause the entire system using the database to log reconstruction to be slow .

 

S e even trying the commands in Listing 17 to reconstruct the log the bank does not return to the “ONLINE” state and continue as if it were corrupted , and there is a trick to ultimately recover the database and put it back in production. It is called " jacked-up ".

First we have a database in " Suspect " and we will also reconstruct the file log data , p ortanto is only ha in the folder datafile SQL Server Data File (* . Mdf ). The log file can be deleted for this test. Then we created the database and downloaded the SQL Server service with the commands in Listing 21 .

 

Listing 21 . Creating the base and downloading the SQL Server service

USE [master]

GO

 

CREATE DATABASE [BaseSemLog]

GO

 

USE [BaseSemLog]

GO

 

CREATE TABLE [StudentNotes]

              (

    [StudentName] VARCHAR ( 50 ),             

    [Gang] VARCHAR ( 30 ),                                         

    [Note] float                                         

    );

GO

 

INSERT INTO [StudentNotes]  VALUES ( 'Eduardo' , 'A' , 7.8 );

INSERT INTO [StudentNotes]  VALUES ( 'João' , 'B' , 10 );

GO


SHUTDOWN

GO

 

We delete the file BaseSemLog_log .LDF of p asta of datafiles SQL Server and c olocamos SQL Server again in the air , with the command net start MSSQLSERVER via prompt command. In addition, v erificamos the state that is the bank with the Listing 22 with the result "SUSPECT", as expected.

 

Listing 22 . Creating the base and downloading the SQL Server service

SELECT DATABASEPROPERTYEX ( N ' BaseSemLog ' , N'STATUS ' ) AS N'Status' ;

GO

 

 

Ok. The database is in the "SUSPECT" state and now we are going to apply the " jacked-up ". To do this, turn back the SQL Server service, as previously done , r enomeie the file BaseSemLog .mdf to BaseSemLog_OLD.mdf and c oloque the SQL Service If rver in the air.

The database also presents ra c atom state " SUSPECT " but it exists only in the metadata of SQL Server. In this case, d eletamos and again create the database with the command d to Listing 23 . What will happen here is that it will only update the SQL Server metadata , since the BaseSemLog .mdf files for BaseSemLog_log.LDF no longer exist.

 

Listing 23 . Creating the base and downloading the SQL Server DROP DATABASE BaseSemLog service

USE [master]

GO

 

CREATE DATABASE [BaseSemLog]

GO

 

With the creation of the new database it will create the same data files as the previous situation (* . Mdf and *. Ldf ). Then we stopped the SQL Server service again, paid for the newly created data file called BaseSemLog .mdf , renamed the BaseSemLog_OLD.mdf file to BaseSemLog.mdf and found the SQL Server service on the air.

At this point, we will still find the database in the “ SUSPECT ” state . That's because again, the SQL Server metadata does not match the characteristics of the new BaseSemLog .mdf data file . To correct this we use the commands in Listing 24 showing the result of Figure 1 6 where it relates the database to its metadata .

 

Listing 24 . Creating the base and downloading the SQL Server service

ALTER DATABASE BaseSemLog SET EMERGENCY

ALTER DATABASE BaseSemLog SET SINGLE_USER  

DBCC CHECKDB ( 'BaseSemLog' , REPAIR_ALLOW_DATA_LOSS ) WITH NO_INFOMSGS  

 



Figure 1 6 . Bank in “ SUSPECT ”.

 

Here he makes it clear that the GUID (the database's unique identifier in the metadata ) are not the same. P or so he recreated the file log en Otem also that the service Service Broker was deactivated for this database. However, the data was again released for access . To impart release m multiuser access the bank and vefique m if the data are in the table ( listing 25 and Figure 17 ).

Also note in Figure 1 6 that he asks for the DBCC CHECKDB command to be executed again to check its consistency. Running this command again in the database will have a verification completed without errors, because now the database is, again, intact and ready to be released for production.

 

Listing 25 . Creating the base and downloading the SQL Server service

ALTER DATABASE BaseSemLog SET MULTI_USER  

USE [BaseComLogSuspito]

GO

 

SELECT [StudentName] ,             

                            [Gang] ,

                            [Note]

FROM [StudentNotes]

GO



Figure 17 . Data again accessible in the database.

Conclusion

We worked on this article with the possible causes for a database to enter the “SUSPECT” state and its respective solutions to put the bank back online ( “ ONLINE ”) . We see that the common reasons for a database to be in “ SUSPECT ” mode always start from problems related to the physical structure of the files that make up the database in SQL Server. We also verified the importance that should be taken in seeking the source of corruption / failure of the database file in the log server (both Windows and SQL Server). We reinforce how important it is to have a backup plan tested in the production environment in case any damage occurs, the data loss is the minimum (or none) possible. 

Here are some tips for deciding between restoring a database or trying to recover it.

We exemplify two types of corruption of datafiles , one being that we recover the physically corrupted data log (with the HxD application ) and another example with the data log without recovery. In this last example, we see several options to return the database to the “ ONLINE ” state and leave it enabled for access, even if the log file is no longer present.

In all cases, it is really important to remind the organization that downtime of the environment and possible loss of data will be necessary , so it is important to stay calm, get organized and work on the information that SQL Server (and Windows) has to discover the source of the problem and how to solve it.

In the action plan for this problem , assess whether it depends on a quick fix (like just rebuilding an index) or really depends on a restoration of the database backup, always taking into account the time you will need to leave the banks (or bank ) inaccessible, noting that this can affect r the business of the company.

Database corruption cases will always end up hindering the business flow and, in extreme cases, the company's revenue , as the loss of data will result in systems that fail to calculate or present information, in addition to user data end lost .

Always keep in mind that prevention is better than having to recover a base with corruption. Therefore, periodically run the DBCC CHECKDB command on the databases and interpret its results. If you have large databases in production and the DBCC CHECKD command generates performance degradation, use the PHYSICAL_ONLY option . Microsoft recommends for environments where the volume of information is significant. This option makes the command faster and does not affect the server, however it does not check the FILESTREAM data , which can be checked later.

And if you find yourself in the worst case scenario where there is total loss of data and where the database with problem has no backup to restore , besides all attempts recovery we use here have failed, there is only one exit : r eentrada all the data manually in a new database .

 

 

Links

* http://support2.microsoft.com/kb/967351/en-us (A heavily fragmented file in an NTFS volume may not grow beyond a certain size)

* http://mh-nexus.de/en/hxd/ ( HxD software )
