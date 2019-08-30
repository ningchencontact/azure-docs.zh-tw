---
title: Azure 資源管理員範本
description: 說明如何使用 Azure Resource Manager 的範本來部署資源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 95c127b3a7c9c47c96b292066bf1597a02896806
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166520"
---
# <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

您可以使用 Azure Resource Manager 建立範本, 以定義您想要部署至 Azure 的內容。 範本是 JavaScript 物件標記法 (JSON) 檔案, 其中包含 Azure 解決方案的基礎結構和設定。 透過範本，您可以在整個生命週期中重複部署方案，並確信您的資源會以一致的狀態部署。

此範本使用宣告式語法, 可讓您陳述想要部署的內容, 而不需要撰寫程式設計命令順序來建立。 在範本中, 您可以指定要部署的資源, 以及這些資源的屬性。

## <a name="benefits-of-resource-manager-templates"></a>Resource Manager 範本的優點

Resource Manager 範本提供了數個優點:

* 您可以以群組形式部署、管理及監視方案的所有資源，而不是個別處理這些資源。

* 您可以在整個方案週期重複部署方案，並確信您的資源會部署在一致的狀態中。

* 您可以透過宣告式範本而非指令碼來管理基礎結構。

* 您可以定義之間的相依性，使得以正確的順序部署資源。

下列建議可協助您在使用您的方案時充分利用 Resource Manager。

* 透過 Resource Manager 範本中的宣告式語法定義和部署基礎結構，而非透過命令式指令。

* 在範本中定義所有的部署和設定步驟。 您在設定方案時應該沒有手動步驟。

* 執行命令式指令來管理您的資源，例如啟動或停止應用程式或機器。

* 遵循[Azure Resource Manager 範本最佳做法](template-best-practices.md)。

## <a name="template-deployment-process"></a>範本部署進程

當您部署範本時, Resource Manager 會剖析範本, 並將其語法轉換成 REST API 作業。 它會將這些作業傳送至適當的資源提供者。 例如，當 Resource Manager 收到具有下列資源定義的範本︰

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

它會將定義轉換成下列 REST API 作業，該作業會再傳送給 Microsoft.Storage 資源提供者︰

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-structure"></a>範本結構

範本和資源群組的定義方式全由您決定，方案的管理方式也是如此。 比方說，您可以透過單一範本在單一資源群組中部署三層式應用程式。

![三層式範本](./media/resource-group-overview/3-tier-template.png)

但您不需要在單一的範本中定義整個基礎結構。 通常的合理作法是將您的部署需求分成一組有目標及特定目的的範本。 您可以輕鬆地將這些份本重複使用於不同的方案。 若要部署特定的方案，您會建立連結所有必要範本的主版範本。 下圖顯示如何透過包含三個巢狀範本的父範本部署三層式方案。

![巢狀階層範本](./media/resource-group-overview/nested-tiers-template.png)

如果您想像的階層有不同的生命週期，您可以將這三個階層部署到不同的資源群組。 請注意，資源仍可連結至其他資源群組中的資源。

![階層範本](./media/resource-group-overview/tier-templates.png)

如需巢狀範本的相關資訊，請參閱[透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。

Azure Resource Manager 會分析相依性，確保以正確的順序建立資源。 如果某個資源依賴另一個資源的值 (例如需要儲存體帳戶以供磁碟使用的虛擬機器)，您必須設定相依性。 如需詳細資訊，請參閱 [定義 Azure Resource Manager 範本中的相依性](resource-group-define-dependencies.md)。

您也可以使用範本進行基礎結構的更新。 例如，您可以將資源新增至您的方案，並將組態規則新增至已部署的資源。 如果此範本定義的資源已經存在，則 Resource Manager 會更新現有資源，而不是建立新資產。

當您需要其他作業 (例如安裝不包含在安裝程式的特定軟體) 時，Resource Manager 會提供案例的延伸模組。 如果您已經使用組態管理服務，例如 DSC、Chef 或 Puppet，您可以透過使用擴充功能繼續使用該服務。 如需虛擬機器擴充功能的相關資訊，請參閱[有關虛擬機器擴充功能和功能](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

當您從入口網站建立方案，方案會自動包含部署範本。 您不必從頭建立您的範本，因為您可以從方案的範本開始，並自訂範本以符合您的特定需求。 如需範例，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](./resource-manager-quickstart-create-templates-use-the-portal.md)。 亦可匯出資源群組的目前狀態，或檢視特定部署所用的範本，以擷取現有資源群組的範本。 檢視[匯出的範本](./manage-resource-groups-portal.md#export-resource-groups-to-templates)有助於了解範本語法。

最後，範本會成為應用程式原始碼的一部分。 您可以檢查您的原始程式碼存放庫，並隨著您的應用程式發展加以更新。 您可以透過 Visual Studio 編輯範本。

## <a name="next-steps"></a>後續步驟

如需範本檔案的詳細資訊, 請參閱[瞭解 Azure Resource Manager 範本的結構和語法](resource-group-authoring-templates.md)。

在定義範本之後，您就可以開始將資源部署至 Azure。 若要部署資源，請參閱：

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](resource-group-template-deploy.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](resource-group-template-deploy-cli.md)
* [使用 Resource Manager 範本與 Azure 入口網站來部署資源](resource-group-template-deploy-portal.md)
* [使用 Resource Manager 範本和 Resource Manager REST API 部署資源](resource-group-template-deploy-rest.md)

