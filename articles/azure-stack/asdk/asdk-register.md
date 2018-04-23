---
title: 向 Azure 註冊 ASDK | Microsoft Docs
description: 說明如何向 Azure 註冊 Azure Stack，以啟用市集摘要整合和使用方式報告。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0ee18a7038ea9a4eebb76bc6648b94940d1db5c7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/19/2018
---
# <a name="register-azure-stack-with-azure"></a>向 Azure 註冊 Azure Stack
您可以向 Azure 註冊 Azure Stack 開發套件 (ASDK) 安裝，以便從 Azure 下載市集項目，以及設定向 Microsoft 回報商務資料的功能。 我們建議您註冊，因為它可讓您測試重要的 Azure Stack 功能，例如市集摘要整合和使用方式報告。 註冊 Azure Stack 之後，使用方式會回報給 Azure 商務。 您可以在註冊時所使用的訂用帳戶下看到這項資訊。 然而，ASDK 使用者將不需針對回報的任何使用方式支付費用。


## <a name="register-azure-stack-with-azure"></a>向 Azure 註冊 Azure Stack 
請遵循這些步驟來向 Azure 註冊 ASDK。

> [!NOTE]
> 這些步驟全都必須從可存取具有特殊權限之端點的電腦執行。 對於 ASDK 而言，這就是開發套件主機電腦。 

在使用這些步驟向 Azure 註冊 ASDK 前，請確定您已安裝 Azure Stack PowerShell，並已下載[部署後設定](asdk-post-deploy.md)一文所述的 Azure Stack 工具。 

1. 以系統管理員身分開啟 PowerShell 主控台。  

2. 執行下列 PowerShell 命令來向 Azure 註冊您的 ASDK 安裝 (您將需要同時登入 Azure 訂用帳戶和本機 ASDK 安裝)：

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Connect-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    Set-AzsRegistration `
        -PrivilegedEndpointCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)
