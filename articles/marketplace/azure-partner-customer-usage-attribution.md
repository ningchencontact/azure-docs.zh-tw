---
title: Azure 合作夥伴與客戶使用狀況屬性
description: 有關如何追蹤客戶 Azure Marketplace 解決方案使用狀況的概觀
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: ce862758d97737d16ef26ca7172cad39f8d8336a
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39359510"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure 合作夥伴客戶使用狀況屬性

身為 Azure 的軟體合作夥伴，您的解決方案可能需要 Azure 元件或必須直接部署在 Azure 基礎結構上。  現在，當客戶部署合作夥伴解決方案並佈建其自己的 Azure 資源時，合作夥伴將難以精準了解那些部署的狀態以及對 Azure 成長的影響。 概略提供部署狀況資訊可讓合作夥伴獲得與 Microsoft 銷售團隊相同的資訊，並獲得 Microsoft 合作夥伴計劃的信用額度。   

Microsoft 正在建立的新方法，可協助合作夥伴更有效追蹤客戶在 Azure 上部署您的軟體所產生的 Azure 使用量。 這種新方法是以使用 Azure Resource Manager 來協調 Azure 服務的部署為基礎。

身為 Microsoft 合作夥伴，您可以將 Azure 使用量與您代表客戶佈建的任何 Azure 資源相關聯。  此關聯可以透過 Azure Marketplace、快速入門存放庫、私人 Github 存放庫，甚至是 1 對 1 客戶業務開發來完成。  若要啟用追蹤功能，有兩種可用方法：

- Azure Resource Manager 範本：Azure Resource Manager 範本或解決方案範本可部署 Azure 服務以執行合作夥伴的軟體。 合作夥伴可建立 Azure Resource Manager 範本，其中定義 Azure 解決方案的基礎結構和設定。 建立 Azure Resource Manager 範本，可讓您與您的客戶在整個生命週期中重複方案，並確信您的資源會以一致的狀態部署。 

- Azure Resource Manager API：合作夥伴可直接呼叫 Azure Resource Manager API，來部署 Azure Resource Manager 範本或產生 API 呼叫以直接佈建 Azure 服務。 

## <a name="method-1-azure-resource-manager-templates"></a>方法 1：Azure Resource Manager 範本 

現今許多合作夥伴的解決方案都使用 Azure Resource Manager 範本來部署在客戶的訂用帳戶上。  如果您在 Azure Marketplace 中、GitHub 上或快速入門中，已有可用的 Azure Resource Manager 範本，修改範本以啟用這個新的追蹤方法的程序應該相當簡單。  如果您未使用 Azure Resource Manager 範本，現在這裡有一些連結，可協助您更了解 Azure Resource Manager 範本與其建立方式： 

*   [建立及部署第一個 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [建立 Azure Marketplace 解決方案範本的指南](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>指示：將 GUID 加入至現有的 Azure Resource Manager 範本

加入 GUID 是主要範本檔案的一項修改：
 1. 假設建立 GUID 所產生的值為 eb7927c8-dd66-43e1-b0cf-c346a422063
 2. 開啟 Azure Resource Manager 範本
 3. 在主要範本檔案中加入新的資源。 資源只需要置於 mainTemplate.json 或 azuredeploy.json 中，而非任何巢狀或連結的範本中。
 4. 在 "pid-"之後輸入 GUID，如上所示。

   您應該會看到類似下列的範例：`pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. 檢查範本是否有任何錯誤
 6. 在適當的存放庫中重新發佈範本

## <a name="sample-template-code"></a>範例範本程式碼

```

{ // add this resource to the mainTemplate.json (do not add the entire file)
      "apiVersion": "2018-02-01",
      "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your GUID here
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": []
        }
      }
    } // remove all comments from the file when done

```

## <a name="method-2-azure-resource-manager-apis"></a>方法 2：Azure Resource Manager API

在某些情況下，您可能會想要直接對 Azure Resource Manager REST API 進行呼叫，以部署 Azure 服務。 [Azure 支援多個 SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) 啟用此作業。  您可以使用其中一個 SDK，或直接呼叫 REST API 來部署資源。

如果您使用 Azure Resource Manager 範本，應該使用上述指示來標記您的解決方案。  如果您不是使用 Azure Resource Manager 範本，並進行直接 API 呼叫，仍可標記您的部署以與 Azure 資源的使用量建立關聯。 

**如何使用 Azure Resource Manager API 來標記部署：** 對於此方法，當設計 API 呼叫時，要在要求的使用者代理程式標頭中包含 GUID。 每個供應項目或 SKU 都應加上 GUID。  此字串所需的格式為使用前置詞 pid-，然後包含合作夥伴產生的 GUID。   

>[!Note] 
>用以插入使用者代理程式的 GUID 格式：pid-eb7927c8-dd66-43e1-b0cf-c346a422063     // 在 "pid-" 之後輸入您的 GUID

此字串的格式至關重要。 如果未包含前置詞 "pid-"，便無法查詢資料。 不同的 SDK 會以不同的方式執行此方法。  若要實作此方法，您必須檢閱支援和適用於慣用 Azure SDK 的方法。 

**使用 Python SDK 的範例：** 對於 Python，您必須使用 "config" 屬性。 您只能新增到 UserAgent。 下列是一個範例：

```python

client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
        client.config.add_user_agent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

>由於沒有全域的靜態設定，所以要為每個用戶端個別完成。您可以選擇執行用戶端中心，以確保每個用戶端都會執行。 
>[其他參考資訊](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

如何使用 Azure PowerShell 或 Azure CLI 來標記部署：如果您透過 AzurePowerShell 來部署資源，可以使用下列方法來附加 GUID：

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

若要使用 Azure CLI 來附加 GUID，請設定 AZURE_HTTP_USER_AGENT 環境變數。  您可以在指令碼的範圍內設定此變數，或全域地設定以用於殼層範圍：

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>註冊 GUID/供應項目

GUID 必須註冊後，才能包含在追蹤中。  

範本 GUID 的所有註冊都會透過 Azure Marketplace 雲端合作夥伴入口網站 (CPP) 來完成。 

適用於現今的 [Azure Marketplace](http://aka.ms/listonazuremarketplace)，並可取得 Cloud Partner 入口網站的存取權。

*   合作夥伴必須先在 [CPP 中有一個設定檔](https://docs.microsoft.com/azure/marketplace/become-publisher)，並建議在 Azure Marketplace 或 AppSource 中列出供應項目 
*   合作夥伴可以註冊多個 GUID 
*   合作夥伴也可以為非 Marketplace 解決方案範本/供應項目註冊 GUID

在您將 GUID 加入至範本或使用者代理程式中，並在 CPP 中註冊該 GUID 之後，便會追蹤所有的部署。 

## <a name="verification-of-guid-deployment"></a>驗證 GUID 部署 

在您修改範本並執行測試部署之後，您可以使用下列 PowerShell 指令碼來擷取您已部署並標記的資源。 

您可以用來確認 GUID 是否已成功加入至 Azure Resource Manager 範本。 其不適用於 Azure Resource Manager API 部署。

登入 Azure，然後在執行指令碼之前，選取包含您想要驗證部署的訂用帳戶。 指令碼必須在部署的訂用帳戶內容中執行。

部署的 GUID 與 resourceGroup 名稱都是必要參數。

您可以在[這裡](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1)找到原始的指令碼。

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>建立 GUID 的指導方針

GUID (唯一識別碼) 是有 32 個十六進位數字的唯一參考號碼。 若要建立 GUID，您應該使用 GUID 產生器來建立 GUID 進行追蹤。  您有多個 [GUID 線上產生器](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E)可以使用。

建議您為每個供應項目與散發通道都建立唯一的 GUID。  例如，您若有兩種解決方案，並透過可在 Azure Marketplace 與 GitHub 上取得的範本來部署兩者。  建立四個 GUIDS：

*   Azure Marketplace 中的供應項目 A 
*   GitHub 上的供應項目 A
*   Azure Marketplace 中的供應項目 B 
*   GitHub 上的供應項目 B

依合作夥伴 (Microsoft 合作夥伴識別碼) 排序和 GUID 來完成報告。 

您也可以選擇以更細微的層級來追蹤 GUID (例如，供應項目的變體 SKU)。

## <a name="guidance-on-privacy-and-data-collection"></a>隱私權和資料收集指導方針

合作夥伴應提供訊息，通知其客戶包含 Azure Resource Manager GUID 追蹤的部署，允許 Microsoft 向合作夥伴回報與這些部署相關聯的 Azure 使用量。  以下是一些範例語言。 在指出「合作夥伴」的位置，您應該填入您自己的公司名稱。 此外，合作夥伴應確保語言符合他們自己的資料隱私權和收集原則，包括讓客戶從追蹤排除的選項： 

**針對 Azure Resource Manager 範本部署**

當部署此範本時，Microsoft 可識別合作夥伴軟體安裝以及部署的 Azure 資源。  Microsoft 可讓用來支援軟體的 Azure 資源相互關聯。  Microsoft 會收集這項資訊，以透過其產品提供最佳體驗以及經營業務。 這項資料會遭到收集，並由 Microsoft 隱私權原則控管，網址為 https://www.microsoft.com/trustcenter。 

**對於 SDK 或 API 部署**

當部署合作夥伴軟體時，Microsoft 可識別合作夥伴軟體安裝以及部署的 Azure 資源。  Microsoft 可讓用來支援軟體的 Azure 資源相互關聯。  Microsoft 會收集這項資訊，以透過其產品提供最佳體驗以及經營業務。 這項資料會遭到收集，並由 Microsoft 隱私權原則控管，網址為 https://www.microsoft.com/trustcenter。

## <a name="support"></a>支援

如需協助，請遵循下列步驟：
 1. 請瀏覽支援網頁：[go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975)
 2. 對於與使用量關聯的問題 - 選取 [問題類型]：[Marketplace 上架] 與 [類別]：[其他]，然後按一下 [提出要求]。 
>[!Note]
>對於存取 Azure Marketplace Cloud Partner 入口網站的問題 - 選取 [問題類型]：[Marketplace 上架] 與 [類別]：[存取問題]，然後按一下 [提出要求]。
 3. 在下一個頁面上，填寫必要的欄位，然後按一下 [繼續]。
 4. 在下一個頁面上，完成任意格式的文字欄位。  
 

 
>[!Important] 
>以 **“ISV Usage Tracking”** 填寫事件標題，隨後在任意格式的大型文字欄位中，詳細描述您的問題。  完成表單的其餘部分，然後按一下 [提交]。

## <a name="faqs"></a>常見問題集

**將 GUID 加入至範本的權益為何？**

Microsoft 會提供合作夥伴一個客戶部署範本和深入解析受影響使用量的檢視。  Microsoft 與合作夥伴都可以使用此資訊來加強與銷售團隊之間的合作關係。 Microsoft 與合作夥伴也都可以使用它來取得個別合作夥伴對 Azure 之成長的更一致檢視。 

**誰可以將 GUID 加入至範本？**

追蹤資源的主要目的是將合作夥伴的解決方案連結到客戶的 Azure 使用量。  使用量資料會繫結至合作夥伴的 Microsoft 合作夥伴網路身分識別 (MPN 識別碼)，而且合作夥伴可在 Cloud Partner 入口網站 (CPP) 中使用報告功能。  

**加入 GUID 之後能否予以變更？**
 
是，客戶或實作合作夥伴可自訂範本並能變更或移除該 GUID。 建議合作夥伴主動向其客戶與合作夥伴，描述資源角色與 GUID，以防止移除或編輯該追蹤 GUID。  變更 GUID 只會影響新的部署和資源，現有的部署和資源不受影響。

**何時才能使用報告功能？**

報告搶鮮版 (Beta) 很快就會推出。  報告功能會與雲端合作夥伴入口網站 (CPP) 整合。

**我可以從類似 GitHub 的非 Microsoft 存放庫追蹤部署的範本嗎？**

是，只要有 GUID，當範本部署時，都會追蹤使用量。  
合作夥伴必須要在 Cloud Partner 入口網站中有設定檔，才可註冊在 Azure Marketplace 外部發佈的相關範本。 

**從 Azure Marketplace 和從類似 GitHub 的其他存放庫部署範本，是否有差異？**

是，在 Azure Marketplace 發佈供應項目的合作夥伴可收到從 Azure Marketplace 部署的更詳細資料。  合作夥伴向 Azure Marketplace 入口網站和 Azure 管理入口網站中的客戶公開其供應項目，將獲益良多。 Azure Marketplace 中的供應項目也可為合作夥伴產生潛在客戶。

**假設我要建立自訂範本用於進行個別的客戶業務開發呢？**

您仍可將 GUID 加入至範本。  如果您使用已註冊的現有 GUID，它會包含在報告中。  如果您建立新 GUID，則必須註冊該新 GUID 才能將它納入追蹤。

**客戶也會收到報告嗎？**

客戶目前可在 Azure 管理入口網站內，追蹤其個別資源或客戶所定義資源群組的使用情況。   

**此追蹤方法和數位記錄合作夥伴 (DPOR) 類似嗎？**

這種將部署與使用情況連結至合作夥伴解決方案的新方法，主要目的在提供合作夥伴解決方案與 Azure 使用量的連結機制。  DPOR 可將諮詢 (系統整合者) 或管理 (控服務提供者) 合作夥伴與客戶的 Azure 訂用帳戶建立關聯。   
