---
title: Azure Stack Hub SQL resource provider 1.1.33.0 release notes
titleSuffix: Azure Stack Hub
description: View the releases notes for the Azure Stack Hub SQL resource provider 1.1.33.0 update.
author: sethmanheim

ms.topic: article
ms.date: 1/22/2020
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2020
ROBOTS: NOINDEX

# Intent: As an Azure Stack operator, I want to view the releases notes for the Azure Stack SQL resource provider 1.1.33.0 update.
# Keyword: sql resource provider release notes 1.1.33.0
---


# SQL resource provider 1.1.33.0 release notes

These release notes describe the improvements and known issues in SQL resource provider version 1.1.33.0.

## Build reference
Download the SQL resource provider binary and then run the self-extractor to extract the contents to a temporary directory. The resource provider has a minimum corresponding Azure Stack Hub build. The minimum Azure Stack Hub release version required to install this version of the SQL resource provider is listed below:

> |Minimum Azure Stack Hub version|SQL resource provider version|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[SQL RP version 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Apply the minimum supported Azure Stack Hub update to your Azure Stack Hub integrated system before deploying the latest version of the SQL resource provider.

## New features and fixes
This version of the Azure Stack Hub SQL resource provider includes the following improvements and fixes:

### Fixes

- **SQL resource provider portal extension might choose the wrong subscription**. The SQL resource provider uses Azure Resource Manager calls to determine the first service admin subscription to use, which might not be the *Default Provider Subscription*. If that happens, the SQL resource provider doesn't work normally.

- **SQL hosting server doesn't list hosted databases.** User-created databases might not be listed when viewing tenant resources for SQL hosting servers.

- **Previous SQL resource provider (1.1.30.0) deployment could fail if TLS 1.2 isn't enabled**. Updated the SQL resource provider 1.1.33.0 to enable TLS 1.2 when deploying the resource provider, updating the resource provider, or rotating secrets.

- **SQL resource provider secret rotation fails**. Fixed issue resulting in the following error code when rotating secrets:
`New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## Known issues

- **SQL SKUs can take up to an hour to be visible in the portal**. It can take up to an hour for newly created SKUs to be visible for use when creating new SQL databases.

    **Workaround**: None.

- **Reused SQL logins**. Attempting to create a new SQL login with the same username as an existing login under the same subscription will result in reusing the same login and the existing password.

    **Workaround**: Use different usernames when creating new logins under the same subscription or create logins with the same username under different subscriptions.

- **Shared SQL logins cause data inconsistency**. If a SQL login is shared for multiple SQL databases under the same subscription, changing the login password will cause data inconsistency.

    **Workaround**: Always use different logins for different databases under the same subscription.

- **SQL resource provider fails to add SQL Server Always On listener**. When using the listener IP address of the SQL Server Always On Listener, the SQL resource provider VM can't resolve the listener's host name.

    **Workaround**: Ensure that DNS works correctly to resolve the listener IP to listener host name.

### Known issues for Cloud Admins operating Azure Stack Hub
Refer to the documentation in the [Azure Stack Hub release notes](azure-stack-servicing-policy.md).

## Next steps
[Learn more about the SQL resource provider](azure-stack-sql-resource-provider.md).

[Prepare to deploy the SQL resource provider](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Upgrade the SQL resource provider from a previous version](azure-stack-sql-resource-provider-update.md).
