---
title: Verify scale unit node disk health
description: Learn how to verify scale unit node disk health
author: sethmanheim

ms.topic: how-to
ms.date: 11/13/2020
ms.author: sethm
ms.reviewer: 
ms.lastreviewed: 

# Intent: 
# Keyword: 

---

# Verifying scale unit node disk health

1.  Connect to the privileged endpoint (PEP).

    1.  See Privileged Access Workstation and privileged endpoint access for instructions for connecting to the PEP.

    1.  Once connected, enter the PEP session, `Enter-PSSession -Session $pep`.

2.  Get the virtual disk health.

    1.  Run `Get-VirtualDisk -cimsession "S-Cluster"` to verify virtual disk health.

        If the system does not return an **OperationalStatus** of **OK** and a
        **HealthStatus** of **Healthy**, then wait a few minutes and then run
        the command again.
        
        ![Screenshot that shows Windows PowerShell with the 'OperationsStatus' and 'HealthStatus' columns highlighted.](media/image-57.png)
        
    1.  Run `Get-VirtualDisk -cimsession "S-Cluster" | Get-StorageJob` to verify that all running storage jobs are complete.
    
    1.  Verify that no results are returned. If there are jobs still
        running, as shown by **JobState**, or all the jobs are marked as
        complete, then wait another 10 minutes and run the same command
        again. The final state should be that no jobs are listed.
    
    1.  If needed, additional storage health verification steps can be found
        in the [Check the status of virtual disk repair using Azure Stack
        Hub
        PowerShell](../../operator/azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell).
        
