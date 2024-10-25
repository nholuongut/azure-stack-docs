---
title: Use SQL databases
titleSuffix: Azure Stack Hub
description: Learn how to use the SQL Server resource provider to offer SQL databases as a service on Azure Stack Hub.
author: sethmanheim

ms.topic: article
ms.date: 08/02/2022
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019

# Intent: As an Azure Stack operator, I want to use the SQL Server resource provider to offer SQL databases as a service on Azure Stack.
# Keyword: use sql databases azure stack

---

# Use SQL databases on Azure Stack Hub

[!INCLUDE [preview-banner](../includes/sql-mysql-rp-limit-access.md)]

Use the SQL resource provider to offer SQL databases on [Azure Stack Hub](azure-stack-overview.md). After you install the resource provider and connect it to one or more SQL Server instances, you and your users can create:

- SQL databases for cloud-native apps.
- SQL databases for web applications.

Limitations to consider before installing the SQL resource provider:

- Users can only create and manage individual databases. Database server instances aren't accessible to end users. This may limit compatibility with on-premises database apps that need access to master, Temp DB, or to dynamically manage databases.
- Your Azure Stack Hub operator is responsible for deploying, updating, securing, configuring, and maintaining the SQL database servers and hosts. The RP service doesn't provide any host and database server instance management functionality.
- Databases from different users in different subscriptions may be located on the same database server instance. The RP does not provide a mechanism for isolating databases on different hosts or database server instances.
- The RP doesn't provide any reporting on tenant usage of databases.
- You can only move a SQL hosting server to another subscription in global Azure. Azure Stack Hub does not support moving a SQL hosting server to another subscription.
- The RP doesn't monitor the SQL server's health.
- There is no access control on SQL Server's system databases. If your SQL hosting server is a standalone SQL server (not SQL HA), SQL RP uses SQL logins to control users' access to their own databases. However, the SQL logins don't control users' access to system databases. For example, a user trying to restore a database from one of the backups will be able to see all the backup histories on the same hosting server, because the backup history is stored in the msdb database.

For traditional SQL Server workload on premises, a SQL Server virtual machine on Azure Stack Hub is recommended.

## SQL resource provider adapter architecture

The resource provider consists of the following components:

- **The SQL resource provider adapter virtual machine (VM)**, which is a Windows Server VM that runs the provider services.
- **The resource provider**, which processes requests and accesses database resources.
- **Servers that host SQL Server**, which provide capacity for databases called hosting servers.

You must create at least one instance of SQL Server or provide access to external SQL Server instances.

> [!NOTE]
> Hosting servers that are installed on Azure Stack Hub integrated systems must be created from a tenant subscription. They can't be created from the default provider subscription. They must be created from the user portal or by using PowerShell with the appropriate sign-in. All hosting servers are billable VMs and must have licenses. The service admin can be the owner of the tenant subscription.

## Next steps

[Deploy the SQL Server resource provider](azure-stack-sql-resource-provider-deploy.md)
