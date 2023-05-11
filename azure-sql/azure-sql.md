# Azure SQL

## Enable managed identity for Azure SQL

To be able to use managed identity for App Service APIs and Function Apps to authenticate without secret, managed identity is a good approach.
To enable managed identity we have to grant following rights:

```SQL
-- For app service
CREATE USER [{app-service-name}] FROM EXTERNAL PROVIDER;
EXEC sp_addrolemember 'db_datareader','{app-service-name}';
EXEC sp_addrolemember 'db_datawriter','{app-service-name}';
-- For app service staging slot
CREATE USER [{app-service-name}] FROM EXTERNAL PROVIDER;
EXEC sp_addrolemember 'db_datareader','{app-service-name/slots/staging}';
EXEC sp_addrolemember 'db_datawriter','{app-service-name/slots/staging}';
-- For function app
CREATE USER [{functions-app-name}] FROM EXTERNAL PROVIDER;
EXEC sp_addrolemember 'db_datareader','{functions-app-name}';
EXEC sp_addrolemember 'db_datawriter','{functions-app-name}';
```

and if we want the pipeline to be able to run script without knowing the administratorLoginPassword, one can grant the rights for devops:

```SQL
-- For DevOps pipeline
CREATE USER [{DevOps-Pipeline-SP}] FROM EXTERNAL PROVIDER;
EXEC sp_addrolemember 'db_ddladmin','{DevOps-Pipeline-SP}';
EXEC sp_addrolemember 'db_datareader','{DevOps-Pipeline-SP}';
EXEC sp_addrolemember 'db_datawriter','{DevOps-Pipeline-SP}';
```

Similar result can be achieved via running the following script

```shell
.\{ScriptName}.ps1 -serverHostName sqlserver.database.windows.net -databaseName {Name} -username {user-email} -webAppName {web-app-name}
```

```PowerShell
param(
	[string]
	$serverHostName,
	[string]
	$databaseName,
	[string]
	$username,
	[string]
	$webAppName
)

# Create Managed Identity user and grant permissions
$query = "CREATE USER [$webAppName] FROM EXTERNAL PROVIDER;"
$query = $query + "EXEC sp_addrolemember 'db_datareader','$webAppName';"
$query = $query + "EXEC sp_addrolemember 'db_datawriter','$webAppName';"
# Note this allows the identity to read and write all tables.

sqlcmd -S $serverHostName -d $databaseName -G -N -U $username -t 120 -b -Q $query
```

After everything is configured, it is possible to check the SQL setup via query

```SQL
SELECT r.name AS RoleName, m.name AS MemberName
FROM sys.database_principals m
JOIN sys.database_role_members rm ON m.principal_id = rm.member_principal_id
JOIN sys.database_principals r ON rm.role_principal_id = r.principal_id
```
Results

```
RoleName         MemberName
db_owner         dbo
db_ddladmin      {DevOps-Pipeline-SP}
db_datareader    {DevOps-Pipeline-SP}
db_datareader    {app-service-name}
db_datareader    {functions-app-name}
db_datawriter    {DevOps-Pipeline-SP}
db_datawriter    {app-service-name}
db_datawriter    {functions-app-name}
```

## Delete database via Azure DAta Studio

This can be done by executing a T-SQL script that kills any active connections, and then using the DROP DATABASE statement to delete the database.

Run the following query on database:
```SQL
USE master;
GO

DECLARE @DatabaseName NVARCHAR(50) = N'YourDatabaseName';

DECLARE @Sql NVARCHAR(MAX) = N'';

SELECT @Sql = @Sql + N'KILL ' + CONVERT(NVARCHAR(10), session_id) + N';'
FROM sys.dm_exec_sessions
WHERE database_id = DB_ID(@DatabaseName);

EXEC sp_executesql @Sql;

DROP DATABASE [YourDatabaseName];
```

## Delete large amounts of data

```SQL
DECLARE @count AS INT = 1
WHILE(@count > 0)
BEGIN 
 BEGIN TRANSACTION TDelete
    
    ;WITH CTE AS
    (
    SELECT TOP(100000) *
    FROM [DATABASE_TABLE]
    WHERE [FILTER_CONSIDITON]
    )
    DELETE FROM CTE

 COMMIT TRANSACTION TDelete
 
 SELECT @count = COUNT(1) 
 FROM [DATABASE_TABLE]
 
 PRINT 'DELETED 100 000 -> '+ CONVERT(VARCHAR(10),@count) + ' REMAINING'
END;
```

