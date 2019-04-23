# Ivanti - Change Portal Creds
This script can be used to Get or Set new Service Account and Configuration Account credentials

Tested 2018.3

Note: 
1) Use the -LogOnly option to run the script in Log Only mode, which doesn't take any actions on any machines.

# Prerequisites
1) This should be run from the Ivanti Servers where the Management or Personalization roles are installed.

# Parameters
-LogDir : This is the output director for logging.  Also used by the -Email paraemter

-DatabaseServer :  Database Server Location

-DatabaseName : The Database name

-DatabaseConnectionName : New Database Connection Name you want to add

-RemoveDatabaseConnection : The Database Connection you want to remove

-AddOnly : Only Adds the New Database Connection, Doesn't perform the Remove actions

-RemoveOnly : Only Removes the specified Database Connection, Doesn't perform the Add actions

# Functions
## Function Get-DatabaseConnections
This Function will get Ivanti Database Connections.
  Examples:
```
  $Connections = Get-DatabaseConnections

  Get-DatabaseConnections
```

## Function Set-DatabaseConnectio
This Function will Add/Remove a database connection.
  Examples:
 ```
  Set-DatabaseConnection -DatabaseServer "DBServer\Instance" -DatabaseName "DBname" -Type Management -DatabaseConnectionName "ivanti-Management-test" -LogOnly -Verbose

  Set-DatabaseConnection -DatabaseServer "DBServer\Instance" -DatabaseName "DBname" -Type Management -DatabaseConnectionName "ivanti-Management-test" -Verbose

Set-DatabaseConnection -DatabaseServer "DatabaseServer" -DatabaseName "Database Name" -Type Personalization -DatabaseConnectionName "ivanti-Pers-test" -AddOnly -Verbose
  
Set-DatabaseConnection -DatabaseServer "DatabaseServer" -DatabaseName "Database Name" -Type Personalization -DatabaseConnectionName "ivanti-Pers-test" -RemoveDatabaseConnection "NEWPS-Database-Conn" -Verbose
```
