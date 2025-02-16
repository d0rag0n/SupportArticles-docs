---
title: Prerequisites and checklist for resolving connectivity errors
description: Provides prerequisite and checklist before you troubleshoot the SQL Server connectivity issues.
ms.date: 11/14/2021
ms.prod-support-area-path: Connection issues
author: cobibi
ms.author: v-yunhya
ms.prod: sql
---
# Recommended prerequisites and checklist for troubleshooting connectivity issues

_Applies to:_ &nbsp; SQL Server  
_Original KB number:_ &nbsp; 4009936

## Recommended prerequisites

To effectively use these troubleshooters, you may want to gather the following information.

1. The text of the error message and the error codes. Check whether the error is intermittent (happens only sometimes) or consistent (happens all the time).

1. If you have administrator access to SQL Server computer, gather and review current computer settings and service accounts using the following procedure.

    1. Download the latest version of SQLCheck from the [Microsoft SQL Networking GitHub repository](https://github.com/microsoft/CSS_SQL_Networking_Tools/wiki).

    1. Unzip the downloaded file into a folder, for example, *C:\Temp*.

    1. Run the command prompt as an administrator to collect the data and save to a file: for example: `SQLCHECK > C:\Temp\server01.SQLCHECK.TXT`.

    > [!NOTE]
    > If you are troubleshooting connectivity issue from a remote client or troubleshooting linked server queries, run SQLCheck tool on all the systems involved.

1. Application and system event logs from SQL Server and client systems. This can help check if there are any system wide issues occurring on your SQL server.

1. If the connections are failing from an application, the connection string from the application. These are typically found in *Web.config* files for ASP.NET applications.

1. Optionally, you may want to collect and review SQL Server error logs for presence of other error messages, exceptions etc. that may be contributing to connectivity issues with SQL server.

## Quick checklist for troubleshooting connectivity issues

> [!NOTE]
> The list below provides quick things to check for different connectivity issues. Review individual topics for detailed troubleshooting steps.

### Option 1

If you have access to the output of SQLCheck tool from Prerequisites section, review information in various sections in the output file (Computer, Client Security and SQL Server) and address any issues that may be contributing to your problem. See examples below:

|Section in the file |Text to search for |Potential action |Can help troubleshoot (examples) |
|-|-|-|-|
|Computer Information |Warning: Network driver may be out of date. |Check online for new drivers if any. |Various connectivity errors |
|Client Security and Driver Information |Diffie-Hellman cipher suites are enabled. Possible risk of intermittent TLS failures if the algorithm version is different between clients and servers |If you're having intermittent connectivity issues, see [Applications experience forcibly closed TLS connection errors when connecting SQL Servers in Windows](/troubleshoot/windows-server/identity/apps-forcibly-closed-tls-connection-errors).|[An existing connection was forcibly closed by the remote host](tls-exist-connection-closed.md)|
|Client Security and Driver Information |SQL Aliases |If present, ensure they're configured properly and pointing to correct server/IP addresses. |[A network-related or instance-specific error occurred while establishing a connection to SQL Server](network-related-or-instance-specific-error-occurred-while-establishing-connection.md) |
|SQL Server Information |Services of Interest |If your SQL service  isn't started, start it. If you're having issues connecting to a named instance, ensure SQL Server Browser service is started or try restarting browser service. |[A network-related or instance-specific error occurred while establishing a connection to SQL Server](network-related-or-instance-specific-error-occurred-while-establishing-connection.md) |
|SQL Server Information |Domain Service Account Properties |If you configure linked servers from your SQL server and Trust for Del value is set to false, then you can run into authentication issues with your linked server queries. |[Troubleshooting "Login failed for user" errors](login-failed-for-user.md)<br/>Login failed for user 'NT AUTHORITY\ANONYMOUS LOGON'<br/>Login failed for user '(null)' |
|SQL Server Information |SPN does not exist |Check this table to see if SPNs for your SQL server are properly configured and fix any issues identified. |[Cannot generate SSPI context](cannot-generate-sspi-context-error.md)|
|SQL Server Information |Details for SQL Server Instance |Check values of TCP Enabled, TCP Ports etc. to check TCP/IP is enabled on the server side and to check if your SQL default instance is listening on 1433 or a different port.  |Various connectivity errors |

### Option 2

If you aren't able to run SQLCheck on your SQL server computer, consider checking the following items before doing in-depth troubleshooting of your SQL connectivity issue:

1. Make sure that SQL Server is started, and that you see the following message in the SQL Server error log:

    > SQL Server is now ready for client connections. This is an informational message; no user action is required.

   You can use the following command in PowerShell to check the status of SQL Server services on the system:
  
    ```PowerShell
    Get-Service | Where {$_.status -eq 'running' -and $_.DisplayName -match "sql server*"}
    ```

   You can use the following command to search the error log file for the specific string "SQL Server is now ready for client connections. This is an informational message; no user action is required.":
  
    ```Powershell
    Get-ChildItem -Path "c:\program files\microsoft sql server\mssql*" -Recurse -Include Errorlog |select-string "SQL Server is now ready for client connections."
    ```

1. Verify basic connectivity over IP address and check for any abnormalities: `ping -a <SQL Server machine>, ping -a <SQL Server IP address>`. If you notice any issues, work with your network administrator. Alternatively, you can use `Test-NetConnection` in PowerShell:

   ```Powershell
   $servername = "DestinationServer"
   Test-NetConnection -ComputerName $servername
   ```

1. Check whether SQL Server is listening on appropriate protocols by reviewing the error log.

   ```Powershell
    Get-ChildItem -Path "c:\program files\microsoft sql server\mssql*" -Recurse -Include Errorlog |select-string "Server is listening on" , "ready to accept connection on" -AllMatches
   ```

1. Check whether you're able to connect to SQL Server by using a UDL file. If it works, then there may be an issue with the connection string. For instructions on the procedure about UDL test, see [Test OLE DB connectivity to SQL Server by using a UDL file](test-oledb-connectivity-use-udl-file.md). Alternately you can use the following script to create and launch a *UDL-Test.udl* file (stored in the *%TEMP%* folder).

    ```Powershell
    clear
    
    $ServerName = "(local)"
    $UDL_String = "[oledb]`r`n; Everything after this line is an OLE DB initstring`r`nProvider=MSOLEDBSQL.1;Integrated Security=SSPI;Persist Security Info=False;User ID=`"`";Initial Catalog=`"`";Data Source=" + $ServerName + ";Initial File Name=`"`";Server SPN=`"`";Authentication=`"`";Access Token=`"`""
    
    Set-Content -Path ($env:temp + "\UDL-Test.udl") -Value $UDL_String -Encoding Unicode
    
    #open the UDL
    Invoke-Expression ($env:temp + "\UDL-Test.udl")
    ```

1. Check whether you're able to connect to SQL Server from other client systems and different user logins. If you're able to, the issue could be specific to the client or login that is experiencing the issue. Check the Windows event logs on problematic client for more pointers. Also check whether network drivers are up to date.

1. If you're experiencing login failures, make sure that a login (server principal) exists and it has `CONNECT SQL` permissions to SQL Server. In addition, make sure that the default database that's assigned to the login is correct, and that the mapped database principal has `CONNECT` permissions to the database. For more information about how to grant `CONNECT` permissions to the database principal, see [GRANT Database Permissions](/sql/t-sql/statements/grant-database-permissions-transact-sql#:~:text=CONTROL%20SERVER-,CONNECT,-CONNECT%20REPLICATION). For more information about how to grant `CONNECT SQL` permissions to the server principal, see [GRANT Server Permissions](/sql/t-sql/statements/grant-server-permissions-transact-sql#:~:text=CONTROL%20SERVER-,CONNECT%20SQL,-CONTROL%20SERVER). Use the following script to help you identify these permissions:

    ```Powershell
    clear
    ## replace these variables with the login, user, database and server 
    $server_principal = "CONTOSO\JaneK"  
    $database_principal = "JaneK"
    $database_name = "mydb"
    $server_name = "myserver"
    
    Write-Host "`n******* Server Principal (login) permissions *******`n`n"
    sqlcmd -E -S $server_name -Q ("set nocount on; SELECT convert(varchar(32),pr.type_desc) as login_type, convert(varchar(32), pr.name) as login_name, is_disabled,
      convert(varchar(32), isnull (pe.state_desc, 'No permission statements')) AS state_desc, 
      convert(varchar(32), isnull (pe.permission_name, 'No permission statements')) AS permission_name,
      convert(varchar(32), default_database_name) as default_db_name
      FROM sys.server_principals AS pr
      LEFT OUTER JOIN sys.server_permissions AS pe
        ON pr.principal_id = pe.grantee_principal_id
      WHERE is_fixed_role = 0 -- Remove for SQL Server 2008
      and name = '" + $server_principal + "'")
    
    Write-Host "`n******* Database Principal (user) permissions *******`n`n"
    sqlcmd -E -S $server_name -d $database_name -Q ("set nocount on; SELECT convert(varchar(32),pr.type_desc) as user_type, convert(varchar(32),pr.name) as user_name, 
      convert(varchar(32), isnull (pe.state_desc, 'No permission statements')) AS state_desc, 
      convert(varchar(32), isnull (pe.permission_name, 'No permission statements')) AS permission_name 
      FROM sys.database_principals AS pr
      LEFT OUTER JOIN sys.database_permissions AS pe
        ON pr.principal_id = pe.grantee_principal_id
      WHERE pr.is_fixed_role = 0
      and name = '" + $database_principal + "'")
    
    Write-Host "`n******* Server to Database Principal mapping ********`n"
    sqlcmd -E -S $server_name -d $database_name -Q ("exec sp_helplogins '" + $server_principal + "'")
    ```

1. If you're troubleshooting Kerberos related issues, you can use the scripts at [Determine If I Am Connected to SQL Server using Kerberos Authentication](https://github.com/microsoft/CSS_SQL_Networking_Tools/wiki/Determine-If-I-Am-Connected-to-SQL-Server-using-Kerberos-Authentication) to determine if Kerberos is properly configured on your SQL Servers.

## Common connectivity issues

When you've gone through the prerequisite and checklist, see [common connectivity issues](resolve-connectivity-errors-overview.md#common-connectivity-issues) and select the corresponding error message for detailed troubleshooting steps.
