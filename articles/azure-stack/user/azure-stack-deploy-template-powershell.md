---
title: 在 Azure Stack 中使用 PowerShell 部署範本 | Microsoft Docs
description: 使用 PowerShell 將範本部署到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d71df99096e58b3ac7adc920b91891b9a50cd6f7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58479576"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>使用 PowerShell 將範本部署到 Azure Stack

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

您可以使用 PowerShell 將 Azure Resource Manager 範本部署到 Azure Stack。 本文說明如何使用 PowerShell 來部署範本。

## <a name="run-azurerm-powershell-cmdlets"></a>執行 AzureRM PowerShell Cmdlet

此範例使用 **AzureRM** PowerShell Cmdlet 和存放在 GitHub 上的範本。 此範本會建立 Windows Server 2012 R2 Datacenter 虛擬機器。

>[!NOTE]
>嘗試此範例之前，請確定您已經為 Azure Stack 使用者[設定 PowerShell](azure-stack-powershell-configure-user.md)。

1. 移至 [https://aka.ms/AzureStackGitHub](https://aka.ms/AzureStackGitHub)並尋找 **101-simple-windows-vm** 範本。 將範本儲存至下列位置：`C:\templates\azuredeploy-101-simple-windows-vm.json`。
2. 開啟已提高權限的 PowerShell 命令提示字元。
3. 將下列指令碼中的 `username` 和 `password` 取代為您的使用者名稱和密碼，然後執行指令碼：

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"
   
    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    >每次執行此指令碼時，請遞增 `$myNum` 參數的值，避免覆寫您的部署。

4. 開啟 Azure Stack 入口網站，依序選取 [瀏覽]、[虛擬機器]，以尋找您的新虛擬機器 (myDeployment001)。

## <a name="next-steps"></a>後續步驟

- [使用 Visual Studio 部署範本](azure-stack-deploy-template-visual-studio.md)
