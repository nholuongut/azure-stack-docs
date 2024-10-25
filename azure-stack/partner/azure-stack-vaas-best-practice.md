---
title: Azure Stack validation best practices
description: Learn the best practices for validation as a service.
author: sethmanheim
ms.topic: article
ms.date: 12/16/2020
ms.author: sethm
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019

ROBOTS: NOINDEX

# Intent: As an Azure Stack Hub operator, I want to learn the best practices for validation as a service.
# Keyword: azure stack validation best practices

---


# Azure Stack validation best practices

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

This article covers best practices for managing resources in Validation as a service (VaaS). For an overview of VaaS resources, see [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md).

## Solution management

### Naming convention for VaaS solutions

Use a consistent naming convention for all solutions registered in VaaS. For example, the solution name can be constructed from the hardware properties below as follows:

|Product Name | Unique Hardware Element 1 | Unique Hardware Element 2 | Solution Name
|---|---|---|---|
My Solution XYZ |  All Flash | My Switch X01 | MySolutionXYZ_AllFlash_MySwitchX01

### When to create a new VaaS solution

Use the same VaaS solution when running workflows against the same hardware SKU. A new VaaS solution should be created only when there's a change to the hardware SKU.

## Workflow management

### Naming convention for VaaS workflows

Use a consistent naming convention for all VaaS workflow runs. For example, construct a workflow name from the build properties below as follows:

|Build Number (Major) | Date | Solution Size | Workflow Name
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## Next steps

- Learn about [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md)
