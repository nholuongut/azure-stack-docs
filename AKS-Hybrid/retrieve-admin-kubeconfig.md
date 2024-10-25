---
title: Retrieve certificate-based admin kubeconfig
description: Retrieve certificate-based admin kubeconfig in AKS enabled by Arc.
author: nholuong
ms.topic: how-to
ms.date: 01/22/2024
ms.author: nholuong 
ms.lastreviewed: 01/22/2024
ms.reviewer: nholuong

---

# Retrieve certificate-based admin kubeconfig

[!INCLUDE [hci-applies-to-23h2](includes/hci-applies-to-23h2.md)]

This article describes how to retrieve a certificate-based admin kubeconfig.

## Get admin certificate-based kubeconfig

An Arc-enabled Kubernetes cluster admin can retrieve the certificate-based admin kubeconfig using the following command. To run the Azure CLI command, you must have "contributor," "owner," and "Azure Kubernetes Service Hybrid Cluster Admin Role" permissions on the cluster. You must also have the **Microsoft.HybridContainerService/provisionedClusterInstances/listAdminKubeconfig/action** action on the cluster, which is pre-configured in the following built-in role.

```azurecli
az aksarc get-credentials --name 
                       --resource-group 
                       [--admin] 
                       [--context] 
                       [--file] 
                       [--overwrite-existing]
```

| Parameter          | Description                                                                                                                                          |
|--------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--name`           | Name of the Arc-enabled AKS instance.                                                                                                                |
| `--resource-group` | Name of the resource group.                                                                                                                          |
| `--admin`          | Cluster admin credentials.                                                                                                                           |
| `--context`        | If specified, overwrite the default context name. The `--admin` parameter takes precedence over `--context`.                                         |
| `--file`           | Kubernetes configuration file to update. The default is to add a new admin kubeconfig into the default kubeconfig path, which is **~\.kube\config**. |
| `--overwrite-existing` | Overwrites an existing cluster entry with the same name. The default value is `False`. |

For more information, see the documentation for [`az aksarc`](/cli/azure/aksarc#az-aksarc-get-credentials).

## Next steps

[AKS hybrid overview](aks-hybrid-options-overview.md)
