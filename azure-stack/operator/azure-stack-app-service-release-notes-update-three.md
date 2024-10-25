---
title: App Service on Azure Stack Hub Update 3 release notes
description: Learn about improvements, fixes, and known issues in Update 3 for App Service on Azure Stack Hub.
author: sethmanheim
manager: stefsch

ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ROBOTS: NOINDEX

# Intent: As an Azure Stack operator, I want to know the release notes for Azure App Service on Azure Stack Update 3.
# Keyword: app service update 3 azure stack
---

# App Service on Azure Stack Hub Update 3 release notes

These release notes describe improvements, fixes, and known issues in Azure App Service on Azure Stack Hub Update 3. Known issues are divided into three sections: issues directly related to deployment, issues with the update process, and issues with the build (post-installation).

> [!IMPORTANT]
> Apply the 1807 update to your Azure Stack Hub integrated system or deploy the latest Azure Stack Development Kit (ASDK) before deploying Azure App Service 1.3.

## Build reference

The App Service on Azure Stack Hub Update 3 build number is **74.0.13698.31**.

## Prerequisites

Refer to the [Prerequisites for deploying App Service on Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) before beginning deployment.

Before you begin the upgrade of Azure App Service on Azure Stack Hub to 1.3, ensure all roles are ready in the Azure App Service administration in the Azure Stack Hub administrator portal.

![App Service role status](media/azure-stack-app-service-release-notes-update-three/image01.png)

## New features and fixes

Azure App Service on Azure Stack Hub Update 3 includes the following improvements and fixes:

- Support for use of SQL Server Always On for Azure App Service resource provider databases.

- Added new environment parameter to the Create-AADIdentityApp helper script to assist targeting different Azure AD regions.

- Updates to **App Service Tenant, Admin, Functions portals and Kudu tools**. Consistent with Azure Stack Hub portal SDK version.

- Updates **Azure Functions runtime** to **v1.0.11820**.

- Updates to core service to improve reliability and error messaging enabling easier diagnosis of common issues.

- **Updates to the following application frameworks and tools**:
  - Added ASP.NET Core 2.1.2
  - Added NodeJS 10.0.0
  - Added Zulu OpenJDK 8.30.0.1
  - Added Tomcat 8.5.31 and 9.0.8
  - Added PHP Versions:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Added Wincache 2.0.0.8
  - Updated Git for Windows to v 2.17.1.2
  - Updated Kudu to 74.10611.3437
  
- **Updates to underlying operating system of all roles**:
  - [Servicing stack update for Windows Server 2016 for x64-based Systems (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [2018-07 Cumulative Update for Windows Server 2016 for x64-based Systems (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

## Post Update Steps (optional)

For customers wishing to migrate to a contained database for existing Azure App Service on Azure Stack Hub deployments, execute these steps after the Azure App Service on Azure Stack Hub 1.3 update has completed:

> [!IMPORTANT]
> This procedure takes approximately 5-10 minutes. This procedure involves killing the existing database login sessions. Plan for downtime to migrate and validate Azure App Service on Azure Stack Hub post migration
>
>

1. Add [AppService databases (appservice_hosting and appservice_metering) to an Availability group](/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Enable contained database.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Converting a database to partially contained. This step will incur downtime as all active sessions need to be killed.

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    ```

1. Migrate logins to contained database users.

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

**Validate**

1. Check if SQL Server has containment enabled.

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Check existing contained behavior.
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

## Known issues (post-installation)

- Workers are unable to reach file server when App Service is deployed in an existing virtual network and the file server is only available on the private network. This issue is called out in the Azure App Service on Azure Stack Hub deployment documentation.

If you chose to deploy into an existing virtual network and an internal IP address to connect to your file server, you must add an outbound security rule which enables SMB traffic between the worker subnet and the file server. Go to the WorkersNsg in the administrator portal and add an outbound security rule with the following properties:

 * Source: Any
 * Source port range: *
 * Destination: IP addresses
 * Destination IP address range: Range of IPs for your file server
 * Destination port range: 445
 * Protocol: TCP
 * Action: Allow
 * Priority: 700
 * Name: Outbound_Allow_SMB445

## Known issues for cloud admins operating Azure App Service on Azure Stack Hub

Refer to the documentation in the Azure Stack Hub 1807 release notes.

## Next steps

- For an overview of Azure App Service, see [Azure App Service on Azure Stack Hub overview](azure-stack-app-service-overview.md).
- For more information about how to prepare to deploy App Service on Azure Stack Hub, see [Prerequisites for deploying App Service on Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
