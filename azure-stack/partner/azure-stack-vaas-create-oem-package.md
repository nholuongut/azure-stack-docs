---
title: Create an OEM extension package
titleSuffix: Azure Stack Hub
description: Learn how to create an OEM extension package in Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 12/16/2020
ms.author: sethm
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019


ROBOTS: NOINDEX

# Intent: As an Azure Stack Hub operator, I want to learn how to create an OEM package in Azure Stack Hub.
# Keyword: azure stack hub oem extension package

---


# Create an OEM package

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

The Azure Stack Hub OEM extension package is the mechanism by which OEM-specific content is added to Azure Stack Hub infrastructure. The content is used in deployment and operational processes like update, expansion, and field replacement.

## Creating the package

Once created and validated, the OEM extension package can be used in VaaS. Before continuing, ensure that you've completed the steps for [creating an OEM package](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). The package is then submitted to Microsoft along with VaaS test results for signing in the package validation workflow. The following steps detail how to bundle the generated files into a single zip file that VaaS can consume.

1. Identify the following content for the package:
    - A zip file containing the package contents.
    - A manifest file named `oemMetadata.xml`, which should be identical in content to the `metadata.xml` file in the root of the package content.

2. Select the content files and create a zip file:

    ![Zip file contents when creating an OEM extension package](media/vaas-create-oem-package-1.png)
    ![Compress item contents when creating an OEM extension package](media/vaas-create-oem-package-2.png)

3. Rename the resulting file so it's descriptive enough for you to identify it.

## Verifying the contents

To validate the structure of your zip file, inspect it and check that there are no subfolders. The TLD has the zipped contents. A valid package structure is shown below:

> [!IMPORTANT]
> Zipping the parent folder instead of the contents will cause package signing to fail.

![Properly zipped package contents when creating an OEM extension package](media/vaas-create-oem-package-3.png)

The zip file can now be uploaded to VaaS and signed by Microsoft in the package validation workflow.

## Next steps

- [Validate an OEM package](azure-stack-vaas-validate-oem-package.md)
