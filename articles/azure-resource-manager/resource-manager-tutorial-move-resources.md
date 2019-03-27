---
title: 將 Azure 資源移到另一個資源群組 | Microsoft Docs
description: 了解如何將 Azure 資源從某個資源群組移到另一個資源群組，或從某個訂用帳戶移到另一個訂用帳戶。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 62e35a361f2fcf9d6f484b699084a78f66121957
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310092"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group"></a>教學課程：將 Azure 資源移至其他資源群組

了解如何將 Azure 資源從某個資源群組移到另一個資源群組。 您也可以將 Azure 資源從某個 Azure 訂用帳戶移到另一個 Azure 訂用帳戶。 在本教學課程中，您可使用 Resource Manager 範本來部署兩個資源群組和一個儲存體帳戶。 然後在資源群組間移動儲存體帳戶。

![Azure Resource Manager 移動資源圖表](./media/resource-manager-tutorial-move-resources/resource-manager-template-move-resources.png)

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 準備資源。
> * 確認資源可以移動。
> * 移動資源前的檢查清單。
> * 驗證移動作業。
> * 移動資源。
> * 清除資源。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prepare-the-resources"></a>準備資源

已建立範本並且放置於[共用儲存體帳戶](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json)。 此範本會定義兩個資源群組和一個儲存體帳戶。 在部署範本時，您必須提供專案名稱。 專案名稱用來產生唯一的資源名稱。  下列 JSON 擷取自此範本：

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

請注意，在 json 中定義的位置，兩個資源群組分別位於美國東部和美國西部。 儲存體帳戶位於美國東部。 將資源移到另一個位置不同的資源群組時，移動作業不會變更資源的位置。

選取 [試試看] 來開啟 Cloud Shell，然後在 Cloud Shell 內執行 PowerShell 指令碼：

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

等候指令碼順利完成，然後開啟 [Azure 入口網站](https://portal.azure.com)，並確認資源群組和儲存體帳戶都如預期般部署。

> [!NOTE]
> 因為範本會定義兩個資源群組，所以此部署會被視為訂用帳戶層級部署。 入口網站範本部署不支援訂用帳戶層級部署。 因此本教學課程會使用 Azure PowerShell。 Azure CLI 也支援訂用帳戶層級部署。 請參閱[為 Azure 訂用帳戶建立資源群組與資源](./deploy-to-subscription.md)。

## <a name="verify-the-resource-can-be-moved"></a>確認資源可以移動

並非所有資源都可以移動。 本教學課程中使用的資源是儲存體帳戶，可以移動。 若要驗證資源是否可以移動，請參閱[可以移動的服務](./resource-group-move-resources.md#services-that-can-be-moved)。

## <a name="checklist-before-moving-resources"></a>移動資源前的檢查清單

這是本教學課程的選擇性步驟 (因為已完成)。

在移動資源之前，有幾個重要步驟需要進行。 請參閱[移動資源前的檢查清單](./resource-group-move-resources.md#checklist-before-moving-resources)。

## <a name="validate-the-move"></a>驗證移動

驗證移動是本教學課程的選擇性步驟 (因為已完成)。

驗證移動作業可讓您直接測試移動案例，而不需要實際移動資源。 您可以使用這項作業來判斷移動是否會成功。 如需詳細資訊，請參閱[驗證移動](./resource-group-move-resources.md#validate-move)。

## <a name="move-the-resource"></a>移動資源

儲存體帳戶位於來源資源群組 (rg1) 內，執行下列 PowerShell 指令碼，將資源移到目的地資源群組 (rg2)。 請務必使用與您部署資源時使用的相同專案名稱。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

開啟 [Azure 入口網站](https://portal.azure.com)、確認儲存體帳戶已移到另一個資源群組，同時確認儲存體帳戶位置仍是美國東部。

移動資源時，在此作業期間會同時鎖定來源群組和目標群組。 資源群組上的寫入和刪除作業將會封鎖，直到移動完成。 此鎖定表示您無法新增、更新或刪除資源群組中的資源，但不表示資源已遭到凍結。 例如，如果您將 SQL Server 和其資料庫移至新的資源群組，使用該資料庫的應用程式不會發生停機時間。 它仍可對資料庫讀取和寫入。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
2. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
3. 選取來源資源群組名稱。  
4. 從頂端功能表中選取 [刪除資源群組]。
5. 選取目的地資源群組名稱。  
6. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何將儲存體帳戶從某個資源群組移到另一個資源群組。 到目前為止，您已處理一個儲存體帳戶或多個儲存體帳戶執行個體。 在下一個教學課程中，您會開發具有多個資源與多個資源類型的的範本。 某些資源有相依的資源。

> [!div class="nextstepaction"]
> [建立相依資源](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
