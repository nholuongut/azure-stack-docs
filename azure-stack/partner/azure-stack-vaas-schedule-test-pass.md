---
title: Schedule a test in Azure Stack Hub Validation portal
titleSuffix: Azure Stack Hub
description: Learn how to schedule a test in the Azure Stack Hub Validation portal.
author: sethmanheim
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sethm
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019


ROBOTS: NOINDEX

# Intent: As an Azure Stack Hub user, I want to learn how to use the Azure Stack Hub Validation portal to schedule my first test.
# Keyword: azure stack hub validation schedule test

---


# Scheduling a test

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Schedule a test in the Microsoft Azure Stack Validation portal for your Azure Stack Hub solution. A validation as a service (VaaS) solution represents an Azure Stack Hub solution with a particular hardware bill of materials (BoM). You can schedule a test to check that your hardware can run Azure Stack Hub.

To check your solution, create the workflow for a test. A VaaS workflow operates within the context of a VaaS solution. It represents a set of test suites that exercise the functionality of an Azure Stack Hub deployment on your hardware. Add your solution's environmental parameters and select one or more tests to run on your solution.

While the Test Pass workflow can be used to run any test provided by VaaS, including tests from the validation workflows, results from the Test Pass workflow aren't considered *official*. For information about official validation workflows, see [Workflows](azure-stack-vaas-key-concepts.md#workflows).

## Prerequisites

Before you follow this quickstart, finish the following tasks:

- [Set up your validation as a service resources](azure-stack-vaas-set-up-resources.md).
- [Deploy the local agent](azure-stack-vaas-local-agent.md) (required).
- [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md) (required).

## Start a workflow

![Sign into the VaaS portal](media/vaas_portalsignin.png)

Sign in to the portal, select or create a solution, and then select the solution.

1. Sign in to the [VaaS portal](https://azurestackvalidation.com).
2. Type the name of an existing solution or select **New solution** to create a new solution. For instructions, see [Create a solution in the VaaS portal](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-azure-stack-hub-validation-portal).
3. Select **Start** on the **Test Passes** tile.

## Specify parameters

![Specify parameters in the VaaS portal](media/vaas_test_pass_parameters.png)

Provide parameters that apply to all tests within the workflow.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Select **Next** to select tests to schedule.

## Select tests to run

The tests you select will be scheduled once the workflow is created.

1. Select the test(s) you want to run in your workflow.

    If you want to override the common parameters (the parameters provided in the previous section) for any test, select the **Edit** link next to specify new values.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. Select **Next** to review the workflow.

## Review and submit

Finish workflow creation.

1. Review the displayed information.

    The service creates your workflow with the provided information and the selected tests will be scheduled.

    If anything appears incorrect, use the **Previous** buttons to go to an earlier section.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## Next steps

- [Monitor and manage tests in the VaaS portal](azure-stack-vaas-monitor-test.md)
