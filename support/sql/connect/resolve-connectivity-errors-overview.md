---
title: Troubleshoot connectivity issues in SQL Server
description: Provides overview of the common connectivity issues in SQL Server and how to use the tools to troubleshoot the issues.
ms.date: 11/14/2021
ms.prod-support-area-path: Connection issues
author: cobibi
ms.author: v-yunhya
ms.prod: sql
---
# Troubleshoot connectivity issues in SQL Server

_Applies to:_ &nbsp; SQL Server  
_Original KB number:_ &nbsp; 4009936

You may experience connectivity issues in SQL Server because of various causes. This article series help you troubleshoot the most common SQL Server connectivity issues, and describes the tools and methods that you can use for troubleshooting.

> [!NOTE]
> If multiple SQL Server instances in your environment are impacted or the issue is intermittent, it usually indicates that Windows policy or networking issue. Therefore, although these series of articles can provide some guidance, it may not effectively troubleshoot those scenarios.

## Preparation

Before you start troubleshooting, check [Recommended prerequisites and checklist for troubleshooting connectivity issues](resolve-connectivity-errors-checklist.md) for logs that you should collect to assist with troubleshooting and a list of quick actions you can take to avoid common connectivity errors when working with SQL Server.

## Common connectivity issues

Use the list below to navigate to the appropriate topic page for detailed troubleshooting steps for your scenario:

- [A network-related or instance-specific error occurred while establishing a connection to SQL Server](network-related-or-instance-specific-error-occurred-while-establishing-connection.md)

- [Cannot generate SSPI context](cannot-generate-sspi-context-error.md)

- [Login failed for user](login-failed-for-user.md)

  Covers the following Login errors:
  - > Login failed for user 'NT AUTHORITY\ANONYMOUS LOGON'
  - > Login failed for user '(null)'
  - > Login failed for user is empty
  - > Login failed for user '\<username\>'
  - > Login failed for user '\<domain\>\\\<username\>'
  - > Login failed for user '\<domainname\>\\\<computername$\>'

- [Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding](timeout-expired-error.md)

- [An existing connection was forcibly closed by the remote host](tls-exist-connection-closed.md)

- [Token-based server access validation failed with an infrastructure error. Check for previous errors](cannot-connect-remotely.md)

- [The certificate chain was issued by an authority that is not trusted](error-message-when-you-connect.md)

## Tools and methods that help you troubleshoot connectivity issues

The following topics provide details on various tools and procedures that are used when troubleshooting various connectivity errors

- [Configure firewalls to work with SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access?context=/troubleshoot/sql/context/context)

  Describes how to configure Windows firewall for successful connections to instances.

- [Test connections to SQL Server by using Universal Data Link (UDL) files](test-oledb-connectivity-use-udl-file.md)
  
  Describes how to test connections between SQL Server and clients using UDL files.

- [Use PortqryUI tool with SQL Server](network-related-or-instance-specific-error-occurred-while-establishing-connection.md#named-instance-of-sql-server)

  Describes how to use the PortqryUI tool, which is a graphical user interface (GUI) port scanner to help troubleshoot connectivity issues.

- [Check whether SQL Server is listening on dynamic or static ports](static-or-dynamic-port-config.md)

  Provides steps to check whether SQL Server is listening on dynamic or static ports.

- [Use Kerberos Configuration Manager to fix SPN issues](cannot-generate-sspi-context-error.md#fix-the-error-with-kerberos-configuration-manager-recommended)

  Describes how to use Kerberos Configuration Manager to fix Service Principal Name (SPN) issues.

## See also

[CSS SQL Connectivity Wiki](https://github.com/microsoft/CSS_SQL_Networking_Tools/wiki/SSPICLIENT)
