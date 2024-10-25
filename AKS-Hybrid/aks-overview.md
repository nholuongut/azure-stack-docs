---
title: What is AKS enabled by Azure Arc?
description: Learn about AKS enabled by Azure Arc and available deployment options.
ms.topic: overview
ms.date: 01/30/2024
author: nholuong
ms.author: nholuong 
ms.reviewer: nholuong
ms.lastreviewed: 01/06/2024

---

# What is AKS enabled by Azure Arc?

Azure Kubernetes Service (AKS), enabled by Azure Arc, brings Azure-managed Kubernetes clusters to a variety of Arc-enabled infrastructures from your datacenter to Edge for IoT so you can run applications wherever you need them.

Azure Arc enabled AKS to run both Linux and Windows Kubernetes clusters locally while using Azure for critical management tasks, such as health monitoring and maintenance. Just like AKS in Azure, when you create a Kubernetes cluster, AKS also creates a Kubernetes control plane so you don't have to. For more information about Kubernetes basics, see [Kubernetes core concepts for AKS](kubernetes-concepts.md).

To enable AKS on infrastructures with different capabilities and resource characteristics, there are two pre-configured deployment options available. Although there are two deployment options, both are AKS at their core – AKS shares common components, support for Windows and Linux containers, continuously growing support for Arc extensions, and support for the Kubernetes ecosystem.

## Deployment options

The available deployment options are:

- **Azure Kubernetes Service (AKS)**: AKS provides a local version of Azure Kubernetes Service managed from the cloud. With AKS on hyper-converged infrastructure such Azure Stack HCI, you can scale Kubernetes clusters up and down to meet application demands and integrate with hyper-converged fabric features such as software defined networking.
- **Azure Kubernetes Service Edge Essentials**: AKS Edge Essentials lets you run a single Kubernetes cluster on fixed set of hardware or virtual machines, including Windows hosts with very low resources, with the minimum amount of configuration needed to run one cluster.

The following chart can help you to choose the right deployment option for your local environment:

:::image type="content" source="media/aks-overview/aks-chart.png" alt-text="Flowchart showing deployment options." lightbox="media/aks-overview/aks-chart.png":::

## Next steps

To get started with AKS, see the following articles:

- [System requirements](aks-hci-network-system-requirements.md)
- [Get started with AKS Edge Essentials](aks-edge-quickstart.md)
