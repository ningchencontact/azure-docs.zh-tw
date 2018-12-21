---
title: 更新 Azure Stack 使用者訂用帳戶的擁有者 | Microsoft Docs
description: 變更 Azure Stack 使用者訂用帳戶的計費擁有者。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 10/19/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: 46dd9c786fddb2f6ce9fb8a761a1b9f135a82d74
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959191"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>變更 Azure Stack 使用者訂用帳戶的擁有者

Azure Stack 操作員可以使用 PowerShell 來變更使用者訂用帳戶的計費擁有者。 例如，變更擁有者的其中一個原因是取代已離開組織的使用者。   

指派給訂用帳戶的「擁有者」有兩種類型：

- **計費擁有者**：根據預設，計費擁有者係指一種使用者帳戶，會從供應項目取得訂用帳戶，然後擁有該訂用帳戶的計費關係。 此帳戶也是訂用帳戶的系統管理員。 訂用帳戶上只能有一個使用者帳戶獲得這項指定。 計費擁有者通常是組織或小組負責人。 

  請使用 PowerShell Cmdlet [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) 來變更計費擁有者。  

- **透過 RBAC 角色新增的擁有者** - 您可以使用[角色型存取控制](azure-stack-manage-permissions.md) (RBAC) 系統，將**擁有者**角色授與其他使用者。 您可以新增任意數量的其他使用者帳戶，作為計費擁有者的補充擁有者。 其他擁有者也是訂用帳戶的系統管理員，並且有刪除計費擁有者以外訂用帳戶的權限。 

  您可以使用 PowerShell 來管理其他擁有者，請參閱[用以管理角色型存取控制的 Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)。

## <a name="change-the-billing-owner"></a>變更計費擁有者

請執行下列程式碼，來變更使用者訂用帳戶的計費擁有者。 您用來執行指令碼的電腦必須連線到 Azure Stack，並且執行 Azure Stack PowerShell 模組 1.3.0 或更新版本。 如需詳細資訊，請參閱[安裝 Azure Stack PowerShell](azure-stack-powershell-install.md)。 

> [!Note]
>  在多租用戶 Azure Stack 中，新擁有者必須位於與現有擁有者相同的目錄中。 在您可以將訂用帳戶擁有權提供給另一個目錄中的使用者之前，您必須先[邀請使用者以來賓身分存取您的目錄](../active-directory/b2b/add-users-administrator.md)。 

在指令碼執行之前，請先取代指令碼中的下列值： 
 
- **$ArmEndpoint**：指定您環境的 Resource Manager 端點。  
- **$TenantId**：指定您的「租用戶識別碼」。 
- **$SubscriptionId**：指定您的訂用帳戶 ID。
- **$OwnerUpn**：將帳戶指定為 **user@example.com**以新增為新的計費擁有者。  

```PowerShell   
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>後續步驟

[管理角色型存取控制](azure-stack-manage-permissions.md)
