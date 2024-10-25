---
title: How to rotate secrets for the Event Hubs on Azure Stack Hub resource provider
description: Learn how to rotate secrets for the Event Hubs on Azure Stack Hub resource provider
author: sethmanheim
ms.author: sethm
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/15/2020
ms.reviewer: jfggdl
ms.lastreviewed: 10/15/2020
---

# How to rotate secrets for Event Hubs on Azure Stack Hub

This article will show you how to rotate the secrets used by the Event Hubs resource provider.

## Overview and prerequisites

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## Prepare a new TLS certificate

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## Rotate secrets

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## Troubleshooting

Secret rotation should complete successfully without errors. If you experience any of the following conditions in the administrator portal, [open a support request](azure-stack-manage-basics.md#where-to-get-support) for assistance:

   - Authentication issues, including problems connecting to the Event Hubs resource provider.
   - Unable to upgrade resource provider, or edit configuration parameters.
   - Usage metrics aren't showing.
   - Bills aren't being generated.
   - Backups aren't occurring.

## Next steps

For details on rotating your Azure Stack Hub infrastructure secrets, visit [Rotate secrets in Azure Stack Hub](azure-stack-rotate-secrets.md).