---
title: 使用 Azure Stack 原則模組 | Microsoft Docs
description: 瞭解如何將 Azure 訂用帳戶的行為限制為與 Azure Stack 訂用帳戶類似
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: 9fade97bbe783cf156f5b73523bc0834a34df926
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487442"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>使用 Azure Stack 原則模組管理 Azure 原則

*適用範圍：Azure Stack 整合式系統和 Azure Stack 開發套件*

Azure Stack 原則模組可讓您設定 Azure 訂用帳戶，以使用與 Azure Stack 相同的版本和服務可用性。 此模組會使用 [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) PowerShell Cmdlet 來建立 Azure 原則，限制訂用帳戶中可用的資源類型和服務。 您可以使用 [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) Cmdlet，在適當的範圍內建立原則指派。 設定原則後，您可以使用您的 Azure 訂用帳戶來開發 Azure Stack 的目標應用程式。

## <a name="install-the-module"></a>安裝模組

1. 依據[安裝 Azure Stack 的 PowerShell](azure-stack-powershell-install.md) 步驟 1 的說明，安裝必要的 AzureRM PowerShell 模組版本。
2. [從 GitHub 下載 Azure Stack 工具](azure-stack-powershell-download.md)。
3. [設定 PowerShell 以便搭配 Azure Stack 使用](azure-stack-powershell-configure-user.md)。
4. 匯入 AzureStack.Policy.psm1 模組：


   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>將原則套用至 Azure 訂用帳戶

您可以使用下列命令，針對 Azure 訂用帳戶套用預設 Azure Stack 原則。 執行此命令前，以您的 Azure 訂用帳戶名稱取代 `Azure subscription name`：

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>將原則套用至資源群組

您可能想要套用更細微的原則。 例如，您在相同的訂用帳戶中可能有其他正在執行的資源。 您可以將原則應用程式範圍設定為特定的資源群組，讓您使用 Azure 資源測試 Azure Stack 的應用程式。 執行下列命令前，以您的 Azure 訂用帳戶名稱取代 `Azure subscription name`：

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>動作中的原則

部署 Azure 原則後，當您嘗試部署原則所禁止的資源時會收到錯誤：

![資源部署結果因原則限制而失敗](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 部署範本](azure-stack-deploy-template-powershell.md)
* [使用 Azure CLI 部署範本](azure-stack-deploy-template-command-line.md)
* [使用 Visual Studio 部署範本](azure-stack-deploy-template-visual-studio.md)
