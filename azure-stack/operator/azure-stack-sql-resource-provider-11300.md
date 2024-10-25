---
title: Azure Stack Hub SQL resource provider 1.1.30.0 release notes
titleSuffix: Azure Stack Hub
description: View the releases notes for the Azure Stack Hub SQL resource provider 1.1.30.0 update.
author: sethmanheim

ms.topic: article
ms.date: 1/22/2020
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ROBOTS: NOINDEX

# Intent: As an Azure Stack operator, I want to view the releases notes for the Azure Stack SQL resource provider 1.1.30.0 update.
# Keyword: sql resource provider release notes 1.1.30.0
---


# SQL resource provider 1.1.30.0 release notes

These release notes describe the improvements and known issues in SQL resource provider version 1.1.30.0.

## Build reference
Download the SQL resource provider binary and then run the self-extractor to extract the contents to a temporary directory. The resource provider has a minimum corresponding Azure Stack Hub build. The minimum Azure Stack Hub release version required to install this version of the SQL resource provider is listed below:

> |Minimum Azure Stack Hub version|SQL resource provider version|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Apply the minimum supported Azure Stack Hub update to your Azure Stack Hub integrated system before deploying the latest version of the SQL resource provider.

## New features and fixes
This version of the Azure Stack Hub SQL resource provider includes the following improvements and fixes:

- **Telemetry enabled for SQL resource provider deployments**. Telemetry collection has been enabled for SQL resource provider deployments. Telemetry collected includes resource provider deployment, start and stop times, exit status, exit messages, and error details (if applicable).

- **TLS 1.2 encryption update**. Enabled TLS 1.2-only support for resource provider communication with internal Azure Stack Hub components. 

### Fixes

- **SQL resource provider Azure Stack Hub PowerShell compatibility**. The SQL resource provider has been updated to work with the Azure Stack Hub 2018-03-01-hybrid PowerShell profile and to provide compatibility with AzureRM 1.3.0 and later.

- **SQL login change password blade**. Fixed an issue where the password can't be changed on the change password blade. Removed links from password change notifications.

- **SQL hosting server settings blade update**. Fixed an issue where the settings blade was incorrectly titled as "Password".

## Known issues

- **SQL SKUs can take up to an hour to be visible in the portal**. It can take up to an hour for newly created SKUs to be visible for use when creating new SQL databases.

    **Workaround**: None.

- **Reused SQL logins**. Attempting to create a new SQL login with the same username as an existing login under the same subscription will result in reusing the same login and the existing password.

    **Workaround**: Use different usernames when creating new logins under the same subscription or create logins with the same username under different subscriptions.

- **Shared SQL logins cause data inconsistency**. If a SQL login is shared for multiple SQL databases under the same subscription, changing the login password will cause data inconsistency.

    **Workaround**: Always use different logins for different databases under the same subscription.

- **TLS 1.2 support requirement**. If you try to deploy or update the SQL resource provider from a computer where TLS 1.2 isn't enabled, the operation might fail. Run the following PowerShell command on the computer being used to deploy or update the resource provider to verify that TLS 1.2 is returned as supported:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  If **Tls12** isn't included in the output of the command, TLS 1.2 isn't enabled on the computer.

    **Workaround**: Run the following PowerShell command to enable TLS 1.2 and then start the resource provider deployment or update script from the same PowerShell session:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```

- **SQL resource provider fails to add SQL Server Always On listener**. When using the listener IP address of the SQL Server Always On Listener, the SQL resource provider VM can't resolve the listener's host name.

    **Workaround**: Ensure that DNS works correctly to resolve the listener IP to listener host name.

### Known issues for Cloud Admins operating Azure Stack Hub
Refer to the documentation in the [Azure Stack Hub release notes](azure-stack-servicing-policy.md).

## Next steps
[Learn more about the SQL resource provider](azure-stack-sql-resource-provider.md).

[Prepare to deploy the SQL resource provider](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Upgrade the SQL resource provider from a previous version](azure-stack-sql-resource-provider-update.md).
