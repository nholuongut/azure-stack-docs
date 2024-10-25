---
title: Validation as a service key concepts
titleSuffix: Azure Stack Hub
description: Learn the key concepts of Azure Stack Hub validation as a service.
author: sethmanheim
ms.topic: article
ms.date: 12/16/2020
ms.author: sethm
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019

ROBOTS: NOINDEX

# Intent: As an Azure Stack Hub user, I want to learn the key concepts of Azure Stack Hub validation as a service.
# Keyword: azure stack hub VaaS validation

---


# Validation as a Service key concepts

This article describes key concepts in validation as a service (VaaS).

## Solutions

A VaaS solution represents an Azure Stack Hub solution with a particular hardware bill of materials (BoM). The VaaS solution acts as a container for the workflows that run against the Azure Stack Hub solution.

### Create a solution in the Azure Stack Hub Validation portal

1. Sign in to the [Azure Stack Hub Validation portal](https://azurestackvalidation.com).
2. On the solutions dashboard, select **New solution**.
3. Enter a name for the solution. For naming suggestions, see [Naming convention for VaaS solutions](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Select **Save** to create the solution.

## Workflows

A VaaS workflow operates within the context of a VaaS solution. It represents a set of test suites that exercise the functionality of an Azure Stack Hub deployment. A workflow should be created for every deployment or software update of an Azure Stack Hub solution.

Workflows are categorized by testing scenario type. In unofficial testing, the **Test Pass** workflow lets you select tests from all available VaaS collateral. In official testing, the **validation** workflows target specific testing scenarios selected by Microsoft.

![VaaS workflow tiles](media/tile_all-workflows.png)

> [!NOTE]
> The **Package Validation** workflow currently supports two scenarios: [Validate OEM packages](azure-stack-vaas-validate-oem-package.md) and [Validate software updates from Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

For more information on workflow types, see [What is validation as a service for Azure Stack Hub?](azure-stack-vaas-overview.md).

### Getting started with VaaS workflows

1. On the solutions dashboard, create a new solution or select an existing one. This refreshes and activates the workflow tiles.
2. To create a new workflow, select **Start** on any tile. For information specific to each workflow, see the following articles:
    - Test Pass: [Quickstart: Use the Azure Stack Hub Validation portal to schedule your first test](azure-stack-vaas-schedule-test-pass.md)
    - Solution Validation: [Validate a new Azure Stack Hub solution](azure-stack-vaas-validate-solution-new.md)
    - Package Validation (monthly update): [Validate software updates from Microsoft](azure-stack-vaas-validate-microsoft-updates.md)
    - Package Validation (package signing): [Validate OEM packages](azure-stack-vaas-validate-oem-package.md)

3. To manage or monitor an existing workflow, select on **Manage** on the workflow tile. Select the name of the workflow and use the **Edit** button to view properties or change common test parameters.

For more information about workflow properties and parameters, see [Workflow common parameters for Azure Stack Hub validation as a service](azure-stack-vaas-parameters.md).

## Tests

A test in VaaS consists of a suite of operations that run against an Azure Stack Hub solution. Tests have different intended purposes identified by a category (like functional or reliability) and target one or more services of Azure Stack Hub. Each test defines its own set of parameters, some of which are specified by common parameters of the containing workflow.

For more information about managing and monitoring tests, see [Monitor and manage tests in the Azure Stack Hub Validation portal](azure-stack-vaas-monitor-test.md).

For more information about test parameters, see [Workflow common parameters for Azure Stack Hub validation as a service](azure-stack-vaas-parameters.md).

## Agents

A VaaS agent drives test execution. Two types of agents run VaaS tests:

- The **cloud agent**. This is the default agent available in VaaS. No setup is required, but this requires in-bound connectivity to your environment and Azure Stack Hub endpoints must be resolvable from the internet. Some tests aren't compatible with the cloud agent.
- A **local agent**. This lets you run validation in scenarios where in-bound connectivity to your environment isn't feasible. Some tests require execution through the local agent.

Local agents aren't tied to any particular Azure Stack Hub or VaaS solution. As a best practice, they should run outside of an Azure Stack Hub environment.

For instructions on adding a local agent, see [Deploy the local agent](azure-stack-vaas-local-agent.md).

## Next steps

- [Best practices for validation as a service](azure-stack-vaas-best-practice.md)