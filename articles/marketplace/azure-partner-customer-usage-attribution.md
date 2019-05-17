---
title: Azure 合作夥伴和客戶的使用方式屬性 |Azure Marketplace
description: 有關如何追蹤客戶 Azure Marketplace 解決方案使用狀況的概觀
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: yijenj
ms.openlocfilehash: dcab4d24ca948980f28483fd09f29588e0329b63
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800984"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure 合作夥伴客戶使用狀況屬性

身為 Azure 的軟體合作夥伴，您的解決方案需要 Azure 元件或必須直接部署在 Azure 基礎結構上。 部署合作夥伴解決方案並佈建其自有 Azure 資源的客戶會發現深入了解部署狀態以及對 Azure 成長的影響並不容易。 獲得概略的部署狀況資訊之後，您就可獲得與 Microsoft 銷售團隊相同的資訊，並獲得 Microsoft 合作夥伴計劃的信用額度。

Microsoft 現在提供一個模型，協助合作夥伴以更好的方式追蹤客戶在 Azure 上部署之軟體的 Azure 使用狀況。 這個新方法使用 Azure Resource Manager 來協調 Azure 服務的部署。

身為 Microsoft 合作夥伴，您可以將 Azure 使用狀況與您代表客戶佈建的任何 Azure 資源相關聯。 您可以透過 Azure Marketplace、快速入門存放庫、私人 GitHub 存放庫與一對一客戶支援來構成此關聯關係。 客戶使用方式屬性支援三種部署選項：

- Azure Resource Manager 範本：合作夥伴可以使用 Resource Manager 範本來部署 Azure 服務執行合作夥伴的軟體。 合作夥伴可建立 Resource Manager 範本以定義其 Azure 解決方案的基礎結構與設定。 Resource Manager 範本可讓您與您的客戶在其生命週期中部署您的解決方案。 您可以確信您的資源會以一致的狀態部署。
- Azure Resource Manager API：合作夥伴可直接呼叫 Resource Manager API，來部署 Resource Manager 範本或產生 API 呼叫以直接佈建 Azure 服務。
- Terraform:合作夥伴可以使用雲端的 orchestrator，例如 Terraform 部署 Resource Manager 範本，或直接將 Azure 服務部署。

客戶使用方式屬性為 新的部署，並不支援標記已部署的現有資源。

客戶使用方式屬性，並要求[Azure 應用程式](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)： 發佈到 Azure Marketplace 的解決方案範本供應項目。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>使用 Resource Manager 範本
許多合作夥伴解決方案都使用 Resource Manager 範本來部署在客戶的訂用帳戶上。 如果您有 Resource Manager 範本可在 Azure Marketplace 中，在 GitHub 上，或快速入門中，修改您的範本，讓客戶使用方式屬性的程序應直接。

如需有關建立及發佈「解決方案範本」的詳細資訊，請參閱

* [建立及部署第一個 Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。
* [Azure 應用程式供應項目](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)。
* 視訊：[為 Azure Marketplace 建置解決方案範本和受控應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603) \(英文\)。


## <a name="add-a-guid-to-your-template"></a>將 GUID 新增到您的範本

若要新增全域唯一識別碼 (GUID)，您只需要在主要範本檔案中進行一處修改：

1. 使用建議的方法[建立 GUID](#create-guids) 並[註冊 GUID](#register-guids-and-offers)。

1. 開啟 Resource Manager 範本。

1. 在主要範本檔案中加入新的資源。 資源只需要置於 **mainTemplate.json** 或 **azuredeploy.json** 檔案中，而非任何巢狀或連結的範本中。

1. 在 **pid-** 前置詞後方輸入 GUID 值 (例如 pid-eb7927c8-dd66-43e1-b0cf-c346a422063)。

1. 檢查範本是否有任何錯誤。

1. 在適當的存放庫中重新發佈範本。

1. [驗證範本部署中的 GUID 已成功建立](#verify-the-guid-deployment)。

### <a name="sample-resource-manager-template-code"></a>範例 Resource Manager 範本程式碼

若要啟用範本的追蹤資源，您必須在資源區段下新增下列其他資源。 在將下列範例程式碼新增至主要範本檔案時，請務必使用您自己的輸入對該範例進行修改。
資源只需要新增至 **mainTemplate.json** 或 **azuredeploy.json** 檔案，不可新增至任何巢狀或連結的範本。

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>使用 Resource Manager API

在某些情況下，您可能會想要直接對 Resource Manager REST API 進行呼叫，以部署 Azure 服務。 [Azure 支援多個 SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) 以啟用此作業。 您可以使用其中一個 SDK，或直接呼叫 REST API 來部署資源。

如果您使用 Resource Manager 範本，應該依照稍早的指示標記您的解決方案。 如果您不是使用 Resource Manager 範本，並進行直接 API 呼叫，仍可標記您的部署以與 Azure 資源的使用量建立關聯。

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>使用 Resource Manager API 標記部署

若要啟用客戶使用量免署名，當您設計您的 API 呼叫，會包含在要求中的使用者代理標頭中的 GUID。 針對每個供應項目或 SKU 新增 GUID。 使用 **pid-** 前置詞設定字串格式，並包括合作夥伴產生的 GUID。 以下是可插入到使用者代理程式中的 GUID 格式範例：

![範例 GUID 格式](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> 此字串的格式至關重要。 若未包括 **pid-** 前置詞，就無法查詢資料。 不同的 SDK 會以不同的方式追蹤。 若要實作此方法，請檢閱支援和適用於慣用 Azure SDK 的方法。

#### <a name="example-the-python-sdk"></a>範例：Python SDK

針對 Python，請使用 **config** 屬性。 您只能將屬性新增到 UserAgent。 以下是範例：

![將屬性新增到使用者代理程式](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> 為每個用戶端新增屬性。 沒有全域靜態設定。 您可以標記用戶端處理站，以確定每個用戶端都在正在追蹤。 如需詳細資訊，請參閱 [GitHub 上的此用戶端處理站範例](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)。

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>使用 Azure PowerShell 標記部署

若您透過 Azure PowerShell 部署資源，請使用下列方法附加您的 GUID：

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>使用 Azure CLI 標記部署

當您使用 Azure CLI 來附加您的 GUID 時，請設定 **AZURE_HTTP_USER_AGENT** 環境變數。 您可以在指令碼的範圍內設定此變數。 您也可以針對殼層範圍設定全域變數：

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
如需詳細資訊，請參閱 < [Azure SDK for Go](https://docs.microsoft.com/go/azure/)。

## <a name="use-terraform"></a>使用 Terraform

Terraform 的支援是透過 Azure 提供者的 1.21.0 版本： [ https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019 ](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)。  這項支援適用於所有部署 Terraform，透過其解決方案的合作夥伴，以及部署與計量付費 Azure 提供者的所有資源 (1.21.0 版本或更新版本)。

Terraform azure 提供者加入新的選擇性欄位，稱為[ *partner_id* ](https://www.terraform.io/docs/providers/azurerm/#partner_id)您用來指定追蹤您用於您的方案的 GUID。 這個欄位的值也可以從來源*ARM_PARTNER_ID*環境變數。

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
合作夥伴想要取得其透過追蹤客戶使用量屬性的 Terraform 部署需要執行下列動作：

* 建立的 GUID （針對每個供應項目或 SKU，GUID 應該會新增）
* 更新他們的 Azure 提供者，以設定的值*partner_id* guid （DO NOT 前修正的 GUID，加上"pid-"，只是將它設定為實際的 GUID）

## <a name="create-guids"></a>建立 GUID

GUID 是具有 32 個十六進位數字的參考號碼。 若要建立 GUID 來進行追蹤，您應該使用 GUID 產生器。 Azure 儲存體小組已建立 [GUID 產生器表單](https://aka.ms/StoragePartners)，其會透過電子郵件傳送正確格式的 GUID 給您，且可跨不同追蹤系統重複使用。

> [!Note]
> 強烈建議您改用[Azure 儲存體的 GUID 產生器格式](https://aka.ms/StoragePartners)建立您的 GUID。 如需詳細資訊，請參閱[常見問題集](#faq)。

建議您為每個產品的每個供應項目與散發通道建立唯一的 GUID。 如果您不想要分割報告，您可以讓產品的多個散發通道使用單一 GUID。

若您使用範本部署產品，且該產品在 Azure Marketplace 與 GitHub 上都有提供，您必須建立並註冊 2 個相異的 GUID：

*   Azure Marketplace 中的產品 A
*   GitHub 上的產品 A

報告是由合作夥伴值 (Microsoft 合作夥伴 ID) 與 GUID 所完成。

您也能以更細微的層級來追蹤 GUID (例如，供應項目的變體 SKU)。

## <a name="register-guids-and-offers"></a>註冊 GUID 與供應項目

若要讓客戶使用方式屬性都必須註冊 Guid。

範本 GUID 的所有註冊都會透過 Azure Marketplace 雲端合作夥伴入口網站 (CPP) 來完成。

在您將 GUID 加入至範本或使用者代理程式中，並在 CPP 中註冊該 GUID 之後，便會追蹤所有部署。

1. 適用於 [Azure Marketplace](https://aka.ms/listonazuremarketplace)，並可取得 CPP 的存取權。

   * 合作夥伴必須[在 CPP 中擁有設定檔](https://docs.microsoft.com/azure/marketplace/become-publisher)。 我們鼓勵您在 Azure Marketplace 或 AppSource 中列出該供應項目。
   * 合作夥伴可以註冊多個 GUID。
   * 合作夥伴可以註冊為非 Marketplace 解決方案範本與供應項目註冊 GUID。

1. 登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。

1. 在右上角，選取您的帳戶圖示，然後選取 [發行者設定檔]。

   ![選取發行者設定檔](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. 在 [設定檔] 頁面上，選取 [新增追蹤 GUID]。

   ![選取 [新增追蹤 GUID]](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. 在 [追蹤 GUID] 方塊中，輸入您的追蹤 GUID。 只輸入 GUID，不要輸入 **pid-** 前置詞。 在 [自訂描述] 方塊中，輸入您的供應項目名稱或描述。

   ![[設定檔] 頁面](media/marketplace-publishers-guide/guid-dev-center-login.png)

   ![輸入 GUID 與供應項目描述](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. 若要註冊多個 GUID，請再次選取 [新增追蹤 GUID]。 頁面上會出現額外的方塊。

   ![再次選取 [新增追蹤 GUID]](media/marketplace-publishers-guide/guid-dev-center-example-add.png)

   ![輸入另一個 GUID 與供應項目描述](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. 選取 [ **儲存**]。

   ![選取 [儲存]](media/marketplace-publishers-guide/guid-dev-center-save.png)

在您將 GUID 加入至範本或使用者代理程式中，並在 CPP 中註冊該 GUID 之後，便會追蹤所有部署。

## <a name="verify-the-guid-deployment"></a>驗證 GUID 部署

在您修改範本並執行測試部署之後，您可以使用下列 PowerShell 指令碼來擷取您已部署並標記的資源。

您可以使用指令碼來確認 GUID 是否已成功加入至您的 Resource Manager 範本。 指令碼不適用於 Resource Manager API 部署。

登入 Azure。 選取具有您想要在執行指令碼之前驗證之部署的訂用帳戶。 在部署的訂用帳戶內容中執行指令碼。

部署的 **GUID** 與 **resourceGroup** 名稱是必要參數。

您可以在 GitHub 上取得[原始指令碼](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1)。

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>報告

您可以在合作夥伴中心分析儀表板中的客戶使用方式屬性找到報表。 ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). 若要查看報表，您必須使用合作夥伴中心認證來登入。 如果您遇到任何問題的報表，或登入，建立支援要求，遵循 [取得支援] 區段中的指示。

追蹤範本的清單中選擇下拉式清單中的夥伴關聯型別，以查看報表。

![客戶使用方式屬性的報表](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>通知您的客戶

合作夥伴應通知客戶有關部署供客戶使用方式屬性。 Microsoft 會向合作夥伴回報與這些部署關聯的 Azure 使用狀況。 下列範例包括您可以用來通知客戶有關這些部署的內容。 在範例中，將 \<PARTNER> 取代為您的公司名稱。 合作夥伴應該確保通知符合他們的資料隱私權和收集原則，包括讓客戶從追蹤排除的選項。

### <a name="notification-for-resource-manager-template-deployments"></a>Resource Manager 範本部署的通知

當您部署此範本時，Microsoft 可以透過已部署的 Azure 資源來識別 \<PARTNER> 軟體的安裝。 Microsoft 可讓用來支援軟體的 Azure 資源相互關聯。 Microsoft 會收集這項資訊，以透過其產品提供最佳體驗以及經營業務。 Microsoft 隱私權原則 (位於 https://www.microsoft.com/trustcenter) 規範此資料的收集與控管。

### <a name="notification-for-sdk-or-api-deployments"></a>SDK 或 API 部署的通知

當您部署 \<PARTNER> 軟體時，Microsoft 可以透過已部署的 Azure 資源來識別 \<PARTNER> 軟體的安裝。 Microsoft 可讓用來支援軟體的 Azure 資源相互關聯。 Microsoft 會收集這項資訊，以透過其產品提供最佳體驗以及經營業務。 Microsoft 隱私權原則 (位於 https://www.microsoft.com/trustcenter) 規範此資料的收集與控管。

## <a name="get-support"></a>取得支援

如果您遇到任何問題的報表或登入合作夥伴中心，請使用合作夥伴中心支援小組建立支援要求： [https://partner.microsoft.com/en-US/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

如果您是 Marketplace 上架及/或客戶使用方式屬性需要協助，請遵循下列步驟。

1. 移至[支援連結](https://go.microsoft.com/fwlink/?linkid=844975)。

1. 在 [問題類型] 下，選取 [Marketplace 上線]。

1. 針對您的問題選擇 [類別]：

   - 針對使用狀況關聯問題，請選取 [其他]。
   - 針對 Azure Marketplace CPP 存取問題，請選取 [存取問題]。

     ![選擇問題類別](media/marketplace-publishers-guide/lu-article-incident.png)

1. 選取 [提出要求]。

1. 在下一頁，輸入必要值。 選取 **繼續**。

1. 在下一頁，輸入必要值。

   > [!Important]
   > 在 [事件標題] 方塊中，輸入 **ISV 使用狀況追蹤**。 請詳細描述您的問題。

   ![輸入事件標題的 ISV 使用狀況追蹤](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. 完成表單，然後選取 [提交]。

您也可以從 Microsoft 合作夥伴技術顧問，技術的售前、 部署和應用程式開發案例來了解，並將客戶使用方式屬性接收技術指導。

### <a name="how-to-submit-a-technical-consultation-request"></a>如何提交技術諮詢要求

1. 請瀏覽[ https://aka.ms/TechnicalJourney ](https://aka.ms/TechnicalJourney)。
1. 選取的雲端基礎結構和管理，以及新的頁面會開啟供您檢視技術之旅。
1. 部署服務 下按一下 提交要求 按鈕
1. 使用您的 MSA （MPN 帳戶） 或 （夥伴儀表板帳戶）; 您 AAD 登入根據您的登入認證，將會開啟線上要求表單：
    * 完成/檢閱的連絡資訊。
    * 諮詢詳細資料可能會預先填入，或從下拉式清單中選取。
    * 輸入標題和問題的描述 （提供盡可能詳細的資料越好）。
1. 按一下 [提交]

檢視與在螢幕擷取畫面的逐步指示[ https://aka.ms/TechConsultInstructions ](https://aka.ms/TechConsultInstructions)。

### <a name="whats-next"></a>什麼是下一步

您會設定您的需求的範圍的呼叫所設定的 Microsoft 合作夥伴技術顧問連絡。

## <a name="faq"></a>常見問題集

**將 GUID 加入至範本的優點為何？**

Microsoft 提供其 influenced 的使用方式的合作夥伴與客戶部署自己的解決方案和深入解析的檢視。 Microsoft 與合作夥伴也可以使用此資訊來加強與銷售團隊之間的合作關係。 Microsoft 與合作夥伴可以使用該資料來取得個別合作夥伴對 Azure 成長的更一致檢視。

**加入 GUID 之後能否予以變更？**

是，客戶或實作合作夥伴可自訂範本並能變更或移除該 GUID。 我們建議夥伴主動描述客戶及合作夥伴若要防止移除或編輯的 guid 角色以及與的資源 GUID。 變更 GUID 只會影響新的部署和資源，現有的部署和資源不受影響。

**我可以從類似 GitHub 的非 Microsoft 存放庫追蹤部署的範本嗎？**

是，只要有 GUID，當範本部署時，都會追蹤使用量。 合作夥伴必須擁有在註冊 Azure Marketplace 外部的部署所用的 Guid CPP 中的設定檔。

**客戶也會收到報告嗎？**

客戶可以在 Azure 入口網站內，追蹤其個別資源或客戶所定義資源群組的使用情況。

**是這種方法類似以數位合作夥伴 (DPOR)？**

這種將部署與使用狀況連結至合作夥伴解決方案的新方法，提供將合作夥伴解決方案連結到 Azure 使用量的機制。 DPOR 可將諮詢 (系統整合者) 或管理 (受控服務提供者) 合作夥伴與客戶的 Azure 訂用帳戶建立關聯。

**使用 Azure 儲存體 GUID 產生器表單的好處是什麼？**

Azure 儲存體的 GUID 產生器表單保證會產生所需格式的 GUID。 此外，如果您是使用任何 Azure 儲存體的資料平面追蹤方法，可以利用相同的 GUID 來追蹤 Marketplace 控制平面。 這可讓您運用夥伴屬性的單一整合 GUID，而不需要維護個別的 GUID。

**可以使用私用的自訂 VHD 的 Azure marketplace 解決方案範本供應項目嗎？**

否，您不能。 虛擬機器映像必須來自 Azure Marketplace，請參閱： [ https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines ](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)。

您可以在 marketplace 中使用您的自訂 VHD 建立 VM 供應項目，並將它標示為私用，因此，沒有人可以看到它。 然後參考此解決方案範本中的 VM。

**無法更新*contentVersion*主要範本的屬性？**

可能是在某些情況下使用 TemplateLink 從另一個範本來部署範本時，預期較舊的 contentVersion 因故 bug。 因應措施是使用中繼資料屬性：

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
