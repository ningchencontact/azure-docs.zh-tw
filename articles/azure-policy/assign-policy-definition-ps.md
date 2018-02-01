---
title: "使用 PowerShell 建立原則指派，以識別 Azure 環境中的不相容資源 | Microsoft Docs"
description: "使用 PowerShell 建立 Azure 原則指派，以識別不相容資源。"
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 1/17/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 67c779b96dab088d810d22ad3053ade106aec56a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>使用 PowerShell 建立原則指派，以識別 Azure 環境中的不相容資源

了解 Azure 中合規性的第一個步驟是識別您資源的狀態。 本快速入門會逐步引導您完成程序來建立原則指派，以識別出未使用受控磁碟的虛擬機器。

在此程序結束時，您將會成功識別出未使用受控磁碟的虛擬機器。 它們不符合原則指派的規範。

PowerShell 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本指南會詳細說明使用 PowerShell 建立原則指派，以識別 Azure 環境中的不相容資源。

本指南需要 Azure PowerShell 模組 4.0 版或更新版本。 執行  `Get-Module -ListAvailable AzureRM`  以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

開始之前，請確定已安裝最新版 PowerShell。 如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。


## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，您會建立一個原則指派，並且指派 Audit Virtual Machines without Managed Disks (稽核沒有受控磁碟的虛擬機器) 定義。 此原則定義會識別與原則定義中設定之條件不相容的資源。

依照下列步驟即可建立新的原則指派。

1. 若要確保您的訂用帳戶可搭配資源提供者使用，請註冊 Policy Insights 資源提供者。 若要註冊資源提供者，您必須有權執行資源提供者的註冊動作作業。 這項作業包含在「參與者」和「擁有者」角色中。

    執行下列命令以註冊資源提供者：

    ```
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
```

    當訂用帳戶中有來自資源提供者的資源類型時，您無法取消註冊該資源提供者。

    如需註冊及檢視資源提供者的詳細資訊，請參閱[資源提供者和類型](../azure-resource-manager/resource-manager-supported-services.md)。

2. 在您註冊資源提供者之後，執行下列命令以檢視所有原則定義，並找出您想要指派的原則定義：

    ```powershell
$definition = Get-AzureRmPolicyDefinition
```

    Azure 原則隨附您可以使用的內建原則定義。 內建原則定義如下：

    - 強制執行標籤和其值
    - 套用標籤和其值
    - 需要 SQL Server 12.0 版

3. 接下來，藉由使用 `New-AzureRmPolicyAssignment` Cmdlet 將原則定義指派至所需範圍。

在此教學課程中，對此命令使用下列資訊：

- 顯示原則指派的**名稱**。 在此案例中，使用 Audit Virtual Machines without Managed Disks (稽核沒有受控磁碟的虛擬機器)。
- **原則** – 原則定義，這是您用來建立指派的根基。 在此案例中，即為原則定義 – *Audit Virtual Machines without Managed Disks* (稽核沒有受控磁碟的虛擬機器)
- **範圍** – 範圍會決定在哪些資源或資源群組上強制執行原則指派。 範圍從訂用帳戶到資源群組。 在此範例中，您會將原則定義指派至 **FabrikamOMS** 資源群組。
- **$definition** – 您必須提供原則定義的資源識別碼 – 在此案例中，您會使用原則定義 Audit Virtual Machines without Managed Disks (稽核沒有受控磁碟的虛擬機器) 的識別碼。

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

您現在可以識別不相容的資源，以了解環境的相容性狀態。

## <a name="identify-non-compliant-resources"></a>識別不相容的資源

1. 瀏覽回 Azure 原則登陸分頁。
2. 選取左窗格上的 [相容性]，然後搜尋您建立的 [原則指派]。

   ![原則相容性](media/assign-policy-definition/policy-compliance.png)

   如果有任何現有資源不符合這個新指派的規範，這些資源會顯示在 [不符合規範的資源] 索引標籤之下。

## <a name="clean-up-resources"></a>清除資源

此集合中的其他指南是以本快速入門為基礎。 如果您打算繼續進行後續的教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請藉由執行以下命令來刪除建立的作業：

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您指派原則定義以識別 Azure 環境中的不相容資源。

若要深入了解指派原則，以確保所建立的**未來**資源是相容的，請繼續進行教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./create-manage-policy.md)
