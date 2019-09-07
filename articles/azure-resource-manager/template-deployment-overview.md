---
title: Azure 資源管理員範本
description: 說明如何使用 Azure Resource Manager 的範本來部署資源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4f273a04322246a2b4112c0b5b8a062732bab555
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390736"
---
# <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

隨著移至雲端，許多小組都採用 agile 開發方法。 這些小組會快速地反復查看。 他們需要重複且可靠地將其解決方案部署至雲端。 作業與開發之間的劃分已消失，因為小組需要在單一進程中管理基礎結構和原始程式碼。

這些挑戰的其中一個解決方案是將部署自動化，並使用基礎結構即程式碼的做法。 您可以使用程式碼來定義需要部署的內容，並透過與原始程式碼相同的進程來管理該程式碼。 您會將基礎結構儲存為來源存放庫中的程式碼，並將其設為版本。

Azure Resource Manager 可讓您透過 Resource Manager 範本，將基礎結構當做程式碼來執行。 範本是 JavaScript 物件標記法（JSON）檔案，其中包含 Azure 解決方案的基礎結構和設定。 此範本使用宣告式語法，可讓您陳述想要部署的內容，而不需要撰寫程式設計命令順序來建立。 在範本中，您可以指定要部署的資源，以及這些資源的屬性。

## <a name="benefits-of-resource-manager-templates"></a>Resource Manager 範本的優點

Resource Manager 範本提供了數個優點。 您可以：

* 以群組方式部署、管理及監視方案的所有資源，而不是個別處理這些資源。

* 在整個開發生命週期中重複部署您的解決方案，並確信您的資源會以一致的狀態部署。

* 透過宣告式範本而非腳本來管理您的基礎結構。

* 定義資源之間的相依性，以正確的順序部署它們。

* 立即利用新版本和服務，因為其他方不需要任何中繼工作。

## <a name="template-file"></a>範本檔案

在您的範本內，您可以撰寫可延伸 JSON 功能的[範本運算式](template-expressions.md)。 這些運算式會利用 Resource Manager 所[提供的](resource-group-template-functions.md)函式。

此範本具有下列區段：

* [參數](template-parameters.md)-在部署期間提供值，可讓相同的範本用於不同的環境。

* [變數](template-variables.md)-定義在您的範本中使用的值。

* [使用者定義函數](template-user-defined-functions.md)-建立可簡化範本的自訂函式。

* [資源](resource-group-authoring-templates.md#resources)-指定要部署的資源。

* [輸出](template-outputs.md)-來自已部署資源的傳回值。

## <a name="dependencies"></a>相依性

Azure Resource Manager 會分析相依性，確保以正確的順序建立資源。 如果某個資源依賴另一個資源的值 (例如需要儲存體帳戶以供磁碟使用的虛擬機器)，您必須設定相依性。 如需詳細資訊，請參閱 [定義 Azure Resource Manager 範本中的相依性](resource-group-define-dependencies.md)。

## <a name="conditional-deployment"></a>條件式部署

您可以將資源新增至您的範本，並選擇性地加以部署。 一般來說，您會傳入參數值，指出是否需要部署資源。 如需詳細資訊，請參閱[Resource Manager 範本中的條件式部署](conditional-resource-deployment.md)。

## <a name="create-multiple-instances"></a>建立多個執行個體

您可以使用 copy 元素來指定多個變數、屬性或資源的實例，而不是在您的範本中多次重複的 JSON 區塊。 如需詳細資訊，請參閱[Azure Resource Manager 範本中的資源、屬性或變數反復](resource-group-create-multiple.md)專案。

## <a name="export-templates"></a>匯出範本

您可以藉由匯出資源群組的目前狀態，或查看用於特定部署的範本，來取得現有資源群組的範本。 檢視[匯出的範本](export-template-portal.md)有助於了解範本語法。

當您從入口網站建立方案，方案會自動包含部署範本。 您不必從頭建立您的範本，因為您可以從方案的範本開始，並自訂範本以符合您的特定需求。 如需範例，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](./resource-manager-quickstart-create-templates-use-the-portal.md)。

## <a name="template-deployment-process"></a>範本部署進程

當您部署範本時，Resource Manager 會剖析範本，並將其語法轉換成 REST API 作業。 例如，當 Resource Manager 收到具有下列資源定義的範本︰

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

如果資源已存在於資源群組中，而且要求未包含屬性的更新，則不會採取任何動作。 如果資源存在，但屬性已變更，則會更新現有的資源。 如果資源不存在，就會建立新的資源。 這種方法可讓您安全地重新部署範本，並知道資源保持一致的狀態。

## <a name="template-design"></a>範本設計

範本和資源群組的定義方式全由您決定，方案的管理方式也是如此。 比方說，您可以透過單一範本在單一資源群組中部署三層式應用程式。

![三層式範本](./media/template-deployment-overview/3-tier-template.png)

但您不需要在單一的範本中定義整個基礎結構。 通常的合理作法是將您的部署需求分成一組有目標及特定目的的範本。 您可以輕鬆地將這些份本重複使用於不同的方案。 若要部署特定的方案，您會建立連結所有必要範本的主版範本。 下圖顯示如何透過包含三個巢狀範本的父範本部署三層式方案。

![巢狀階層範本](./media/template-deployment-overview/nested-tiers-template.png)

如果您想像的階層有不同的生命週期，您可以將這三個階層部署到不同的資源群組。 請注意，資源仍可連結至其他資源群組中的資源。

![階層範本](./media/template-deployment-overview/tier-templates.png)

如需巢狀範本的相關資訊，請參閱[透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。

## <a name="next-steps"></a>後續步驟

* 如需範本檔案中屬性的相關資訊，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](resource-group-authoring-templates.md)。
* 若要開始開發範本，請參閱[使用 Visual Studio Code 建立 Azure Resource Manager 範本](resource-manager-tools-vs-code.md)。


