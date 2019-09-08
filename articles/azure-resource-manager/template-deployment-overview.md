---
title: Azure 資源管理員範本
description: 說明如何使用 Azure Resource Manager 的範本來部署資源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802008"
---
# <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

隨著移至雲端，許多小組都採用 agile 開發方法。 這些小組會快速地反復查看。 他們需要將其解決方案重複部署至雲端，並知道其基礎結構處於可靠的狀態。 由於基礎結構已成為反復程式的一部分，因此作業與開發之間的劃分已消失。 小組必須透過統一的程式來管理基礎結構和應用程式程式碼。

若要解決這些挑戰，您可以將部署自動化，並使用基礎結構即程式碼的做法。 在程式碼中，您可以定義需要部署的基礎結構。 基礎結構程式碼會成為專案的一部分。 就像應用程式程式碼一樣，您可以將基礎結構程式碼儲存在來源存放庫中，並將其版本。 小組中的任何人都可以執行程式碼，並部署類似的環境。

若要將基礎結構當做 Azure 解決方案的程式碼來執行，請使用 Azure Resource Manager 範本。 範本是 JavaScript 物件標記法（JSON）檔案，可定義專案的基礎結構和設定。 此範本使用宣告式語法，可讓您陳述想要部署的內容，而不需要撰寫程式設計命令順序來建立。 在範本中，您可以指定要部署的資源，以及這些資源的屬性。

## <a name="benefits-of-resource-manager-templates"></a>Resource Manager 範本的優點

Resource Manager 範本提供下列優點：

* 以群組方式部署、管理及監視方案的所有資源，而不是個別處理這些資源。

* 在整個開發生命週期中重複部署您的解決方案，並確信您的資源會以一致的狀態部署。

* 透過宣告式範本而非腳本來管理您的基礎結構。

如果您嘗試在使用 Resource Manager 範本或其中一個其他基礎結構作為程式碼服務時，請考慮下列優點範本已超過這些服務：

* 新的 Azure 服務和功能會立即在範本中提供。 一旦資源提供者引進新資源，您就可以透過範本來部署這些資源。 透過其他基礎結構即程式碼服務，您必須等候協力廠商為新資源執行介面。

* 範本部署是透過單一範本提交，而不是透過多個命令式命令來處理。 Resource Manager 會協調相互相依資源的部署，使其以正確的順序建立。 它會剖析範本，並根據資源間的參考判斷部署的正確順序。

   ![範本部署比較](./media/template-deployment-overview/template-processing.png)

* 範本部署會在 Azure 入口網站中進行追蹤。 您可以查看部署歷程記錄，並取得範本部署的相關資訊。 您可以看到已部署的範本、傳入的參數值，以及任何輸出值。 其他基礎結構即程式碼服務不會透過入口網站進行追蹤。

   ![部署記錄](./media/template-deployment-overview/deployment-history.png)

* 範本部署會進行預先飛行驗證。 Resource Manager 在開始部署之前檢查範本，以確定部署將會成功。 您的部署較不可能以半完成狀態停止。

* 如果您使用[Azure 原則](../governance/policy/overview.md)，則會在透過範本部署時，在不符合規範的資源上進行原則補救。

* Microsoft 提供部署[藍圖](../governance/blueprints/overview.md)來符合法規和合規性標準。 這些藍圖包括適用于各種架構的預先建立範本。

## <a name="idempotent"></a>等冪

等冪直接表示您可以多次執行相同的作業，並取得相同的結果。 部署 Resource Manager 範本是等冪的。 您可以多次部署相同的範本，並取得相同狀態的相同資源類型。 這個概念很重要，因為這表示您會將範本重新部署至現有的資源群組，或將範本部署到新的資源群組，而得到一致的結果。

我們假設您已將三個資源部署至資源群組，然後決定您需要新增第四個資源。 您可以將第四個資源新增至現有的範本，而不是建立只包含新資源的新範本。 當您將新範本部署至已有三個資源的資源群組時，Resource Manager 找出要採取的動作。

如果資源存在於資源群組中，而且要求未包含屬性的更新，則不會採取任何動作。 如果資源存在，但屬性已變更，則會更新現有的資源。 如果資源不存在，就會建立新的資源。

您確信當部署完成時，資源一律會處於預期的狀態。

## <a name="template-file"></a>範本檔案

在您的範本內，您可以撰寫可延伸 JSON 功能的[範本運算式](template-expressions.md)。 這些運算式會利用 Resource Manager 所[提供的](resource-group-template-functions.md)函式。

此範本具有下列區段：

* [參數](template-parameters.md)-在部署期間提供值，可讓相同的範本用於不同的環境。

* [變數](template-variables.md)-定義在您的範本中重複使用的值。 可以從參數值來進行。

* [使用者定義函數](template-user-defined-functions.md)-建立可簡化範本的自訂函式。

* [資源](resource-group-authoring-templates.md#resources)-指定要部署的資源。

* [輸出](template-outputs.md)-來自已部署資源的傳回值。

## <a name="template-features"></a>範本功能

Resource Manager 會分析相依性，以確保以正確的順序建立資源。 大部分的相依性會以隱含方式決定。 不過，您可以明確地設定相依性，以確保在另一項資源之前部署一個資源。 如需詳細資訊，請參閱 [定義 Azure Resource Manager 範本中的相依性](resource-group-define-dependencies.md)。

您可以將資源新增至您的範本，並選擇性地加以部署。 一般來說，您會傳入參數值，指出是否需要部署資源。 如需詳細資訊，請參閱[Resource Manager 範本中的條件式部署](conditional-resource-deployment.md)。

您可以使用 copy 元素來指定多個變數、屬性或資源的實例，而不是在您的範本中多次重複的 JSON 區塊。 如需詳細資訊，請參閱[Azure Resource Manager 範本中的資源、屬性或變數反復](resource-group-create-multiple.md)專案。

## <a name="export-templates"></a>匯出範本

您可以藉由匯出資源群組的目前狀態，或查看用於特定部署的範本，來取得現有資源群組的範本。 檢視[匯出的範本](export-template-portal.md)有助於了解範本語法。

當您從入口網站建立方案，方案會自動包含部署範本。 您不必從頭建立您的範本，因為您可以從方案的範本開始，並自訂範本以符合您的特定需求。 如需範例，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](./resource-manager-quickstart-create-templates-use-the-portal.md)。

## <a name="template-deployment-process"></a>範本部署進程

當您部署範本時，Resource Manager 會將範本轉換成 REST API 作業。 例如，當 Resource Manager 收到具有下列資源定義的範本︰

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
* 如需 Resource Manager 服務的簡介，包括其管理功能，請參閱[Azure Resource Manager 總覽](resource-group-overview.md)。

