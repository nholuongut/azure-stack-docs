---
title: Use cluster labels in AKS enabled by Azure Arc
description: Learn how to use labels in Kubernetes clusters in AKS enabled by Arc.
ms.topic: how-to
ms.date: 01/18/2024
author: nholuong
ms.author: nholuong 
ms.lastreviewed: 01/18/2024
ms.reviewer: nholuong

---

# Use cluster labels in AKS enabled by Azure Arc

[!INCLUDE [hci-applies-to-23h2](includes/hci-applies-to-23h2.md)]

If you have multiple node pools, you might want to add a label during node pool creation. Kubernetes labels handle the scheduling rules for nodes.

This article describes how to use labels in a Kubernetes cluster on AKS enabled by Arc.

## Create a Kubernetes cluster with a label

1. Create a Kubernetes cluster with a label using the [`az aksarc create`](/cli/azure/aksarc#az-aksarc-create) command and specify the `--node-labels` parameter to set your labels. Labels must be a key/value pair and have [valid syntax](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set).

   ```azurecli
   az aksarc create --resource-group myResourceGroup --name myAKSCluster --node-count 2 --nodepool-labels dept=IT
   ```

1. Verify that the labels were set using the `kubectl get nodes --show-labels` command:

   ```powershell
   kubectl get nodes --show-labels | grep -e "dept=IT"
   ```

## Create a node pool with a label

1. Create a node pool with a label using the [`az aksarc nodepool add`](/cli/azure/aksarc/nodepool#az-aksarc-nodepool-add) command and specify a name for the `--name` parameters and labels for the `--labels` parameter. Labels must be a key/value pair and have [valid syntax](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set).

   The following example creates a node pool named `labelnp` with the labels `dept=HR`:

   ```azurecli
   az aks nodepool add –resource-group myResourceGroup –cluster-name myAKSCluster –name labelnp –node-count 1 –labels dept=HR –no-wait
   ```

   The following example output from the [`az aksarc nodepool list`](/cli/azure/aksarc/nodepool#az-aksarc-nodepool-list) command shows the `labelnp` node pool creates nodes with the specified `nodeLabels`:

   ```json
   [
     {
       ...
       "count": 1,
       ...
       "name": "labelnp",
       "orchestratorVersion": "1.15.7",
       ...
       "provisioningState": "Creating",
       ...
       "nodeLabels":  {
         "costcenter": "5000",
         "dept": "HR"
       },
       ...
     },
    ...
   ]
   ```

1. Verify the labels were set using the `kubectl get nodes --show-labels` command:

   ```powershell
   kubectl get nodes --show-labels | grep -e "dept=HR"
   ```

## Unavailable labels

AKS enabled by Azure Arc follows the same rules as AKS:

- You can't change the reserved system label. If you attempt to do so, an error is returned.
- The Kubernetes and AKS reserved prefixes can't be used for any node.

For more information, see [Unavailable labels](/azure/aks/use-labels#unavailable-labels) in the AKS documentation.

## Next steps

For more information about Kubernetes labels, [see the Kubernetes documentation](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/).
