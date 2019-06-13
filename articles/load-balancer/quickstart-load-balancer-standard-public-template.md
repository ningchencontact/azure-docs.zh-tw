---
title: 快速入門：建立 Standard Load Balancer - Azure Resource Manager 範本
titlesuffix: Azure Load Balancer
description: 本快速入門說明如何使用 Azure Resource Manager 範本建立 Standard Load Balancer。
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 0e47560ca43b23f4779da701f3e6f11f53a6b1ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480395"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>快速入門：使用 Azure Resource Manager 範本建立 Standard Load Balancer 以平衡 VM 的負載

負載平衡會將傳入要求分散於多部虛擬機器，藉此提供高可用性和範圍。 您可以部署 Azure Resource Manager 範本來建立負載平衡器，以平衡虛擬機器 (VM) 的負載。 本快速入門示範如何使用標準負載平衡器來平衡 VM 的負載。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-a-standard-load-balancer"></a>建立標準負載平衡器

在本節中，您會建立標準負載平衡器，以協助平衡虛擬機器的負載。 標準負載平衡器只支援標準公用 IP 位址。 當您建立標準負載平衡器時，也必須建立新的標準公用 IP 位址，而該 IP 位址會設定為標準負載平衡器的前端 (預設的名稱為 LoadBalancerFrontend  )。 有許多方法可用來建立標準負載平衡器。 在本快速入門中，您會使用 Azure PowerShell 來部署 [Resource Manager 範本](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json)。 Resource Manager 範本是 JSON 檔案，該檔案定義您需要為您的解決方案部署的資源。 若要了解部署和管理 Azure 解決方案的相關概念，請參閱 [Azure Resource Manager 文件](/azure/azure-resource-manager/)。 若要尋找更多有關 Azure Load Balancer 的範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

若要部署範本，請選取 [試試看]  以開啟 Azure Cloud Shell，然後將下列 PowerShell 指令碼貼到 Shell 視窗中。 若要貼上程式碼，請以滑鼠右鍵按一下 Shell 視窗，然後選取 [貼上]  。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
$adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword
```

請注意，資源群組名稱是附加 **rg** 的專案名稱。 您會在下一節中用到資源群組名稱。  建立資源需要幾分鐘的時間。

## <a name="test-the-load-balancer"></a>測試負載平衡器

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左側面板中的 [資源群組]  。
1. 選取您在上一節中建立的資源群組名稱。  預設的資源群組名稱是附加 **rg** 的專案名稱。
1. 選取負載平衡器。  只有一個負載平衡器。 預設名稱是附加 **-lb** 的專案名稱。
1. 將公用 IP 位址 (僅 IP 位址的部分) 複製並貼到您瀏覽器的網址列。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

   ![IIS Web 伺服器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

若要查看 Load Balancer 如何將流量分散於所有三部 VM，您可以從用戶端機器中強制重新整理您的網頁瀏覽器。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、負載平衡器和所有相關資源。 若要這樣做，請從 Azure 入口網站選取包含 Load Balancer 的資源群組，然後選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立標準負載平衡器、將 VM 連結到標準負載平衡器、設定負載平衡器流量規則、健康狀態探查，然後測試負載平衡器。 若要深入了解 Azure Load Balancer，請繼續 Azure Load Balancer 的教學課程。

> [!div class="nextstepaction"]
> [Azure Load Balancer 教學課程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
