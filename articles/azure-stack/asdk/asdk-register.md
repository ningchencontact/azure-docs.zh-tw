---
title: 向 Azure 註冊 ASDK | Microsoft Docs
description: 說明如何向 Azure 註冊 Azure Stack，以啟用市集摘要整合和使用方式報告。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 2a447931ea850c4ccbe618270de5fbbc9b9eaea7
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366591"
---
# <a name="azure-stack-registration"></a>Azure Stack 註冊
您可以向 Azure 註冊 Azure Stack 開發套件 (ASDK) 安裝，以便從 Azure 下載市集項目，以及設定向 Microsoft 回報商務資料的功能。 必須註冊才能支援完整的 Azure Stack 功能，包括 Marketplace 摘要整合。 我們建議您註冊，因為它可讓您測試重要的 Azure Stack 功能，例如市集摘要整合和使用方式報告。 註冊 Azure Stack 之後，使用方式會回報給 Azure 商務。 您可以在註冊時所使用的訂用帳戶下看到這項資訊。 然而，ASDK 使用者將不需針對回報的任何使用方式支付費用。

如果您尚未註冊 ASDK，您可能會看到 [需要啟用] 警告警示，提示您註冊 Azure Stack 開發套件。 這是預期的行為。

## <a name="prerequisites"></a>必要條件
在使用這些指示向 Azure 註冊 ASDK 前，請確定您已安裝 Azure Stack PowerShell，並已下載[部署後設定](asdk-post-deploy.md)一文所述的 Azure Stack 工具。

此外，在用來向 Azure 註冊 ASDK 的電腦上，PowerShell 語言模式必須設定為 **FullLanguageMode**。 若要確認目前的語言模式設定為完整，請開啟已提升權限的 PowerShell 視窗，並執行下列 PowerShell 命令：

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

確定輸出有傳回 **FullLanguageMode**。 如果傳回任何其他語言模式，則必須在另一部電腦上執行註冊，或是必須將語言模式設定為 **FullLanguageMode**，才能繼續。

## <a name="register-azure-stack-with-azure"></a>向 Azure 註冊 Azure Stack
請遵循這些步驟來向 Azure 註冊 ASDK。

> [!NOTE]
> 這些步驟全都必須從可存取具有特殊權限之端點的電腦執行。 對於 ASDK 而言，這就是開發套件主機電腦。

1. 以系統管理員身分開啟 PowerShell 主控台。  

2. 執行下列 PowerShell 命令，以向 Azure 註冊 ASDK 安裝。 您必須登入 Azure 訂用帳戶和本機 ASDK 安裝。 如果您還沒有 Azure 訂用帳戶，您可以[在此建立免費的 Azure 帳戶](https://azure.microsoft.com/free/?b=17.06)。 註冊 Azure Stack 不會對您的 Azure 訂用帳戶收取任何費用。  

  ```PowerShell  
  # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
  Add-AzureRmAccount -EnvironmentName "AzureCloud"

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
  ```
3. 當指令碼完成時，您應該會看到此訊息：**您的環境現在已使用提供的參數註冊並啟動。**

    ![您的環境現在已註冊](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>確認註冊已成功
請遵循下列步驟來確認 ASDK 已成功向 Azure 完成註冊。

1. 登入 [Azure Stack 系統管理入口網站](https://adminportal.local.azurestack.external)。

2. 按一下 [市集管理] > [從 Azure 新增]。

    ![](media/asdk-register/2.PNG)

3. 如果您看到 Azure 提供的項目清單，則表示啟用已成功。

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>後續步驟
[新增 Azure Stack 市集項目](.\.\azure-stack-marketplace.md)
