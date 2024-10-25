---
title: Consume monitoring data from Azure Stack Hub 
description: Learn about options for consuming monitoring data from Azure Stack Hub.
author: sethmanheim

ms.topic: article
ms.date: 2/1/2021
ms.author: sethm
ms.lastreviewed: 11/11/2019

# Intent: As an Azure Stack user, I want to consume monitoring data in Azure Stack so I can make decisions based on those metrics.
# Keyword: azure stack monitoring data

---


# Consume monitoring data from Azure Stack Hub

Monitoring data is found in a single location with the Azure Monitor pipeline, just like Azure Monitor in global Azure. But not all of the monitoring data found in global Azure is available in Azure Stack Hub. In this article, we provide a summary of the various ways to consume monitoring data in Azure Stack Hub.
 
## Options for data consumption

| Data type | Category | Supported services | Methods of access |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor platform-level metrics | Metrics | [Supported metrics with Azure Monitor on Azure Stack Hub](azure-stack-metrics-supported.md) | REST API |
| Compute guest OS metrics (for example, Perf count) | Metrics | Windows and Linux VMs | Storage table or blob:<br>Windows or Linux Azure Diagnostics <br>Event Hub:<br>Windows Azure Diagnostics |
| Storage metrics | Metrics | Azure Storage | Storage table:<br>Storage Analytics |
| Activity log | Events | All Azure Services | REST API:<br>Azure Monitor Event API |
| Compute guest OS logs (for example,  IIS, ETW, syslogs) | Events | Windows and Linux VMs | Storage table or blob:<br>Windows or Linux Azure Diagnostics <br>Event Hub:<br>Windows Azure Diagnostics |
| Storage logs | Events | Azure Storage | Storage table:<br>Storage Analytics |

## Next steps

Learn more about [Azure monitor on Azure Stack Hub](azure-stack-metrics-azure-data.md).
