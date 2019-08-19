---
title: 適用于 Azure 受控應用程式建立體驗的 CreateUiDefinition |Microsoft Docs
description: 描述如何建立 Azure 受控應用程式的 UI 定義
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 1ee6d9332a2be5ccb22b7571b348e2e0aae78fb2
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563552"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>適用于 Azure 受控應用程式建立體驗的 CreateUiDefinition

本檔介紹**createUiDefinition**的核心概念, 此檔案 Azure 入口網站在建立受控應用程式時用來定義使用者介面。

範本如下所示

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

CreateUiDefinition 一律會包含三個屬性︰ 

* 處理常式
* 版本
* 參數

處理常式應該一律是`Microsoft.Azure.CreateUIDef`, 而最新支援的版本`0.1.2-preview`是。

parameters 屬性的結構描述取決於指定的處理常式和版本之組合。 針對受控應用程式，支援的屬性為 `basics`、`steps` 和 `outputs`。 basics 和 steps屬性包含要在 Azure 入口網站中顯示的[元素](create-uidefinition-elements.md) - 如同文字方塊和下拉式清單。 outputs 屬性可用來將指定元素的輸出值對應至 Azure Resource Manager 部署範本的參數。

建議包含 `$schema`，但是為選用。 如果指定，`version` 的值必須符合 `$schema` URI 內的版本。

您可以使用 JSON 編輯器來建立 UI 定義, 然後在[Ui 定義沙箱](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)中進行測試以進行預覽。 如需有關沙箱的詳細資訊, 請參閱[測試入口網站介面的 Azure 受控應用程式](test-createuidefinition.md)。

## <a name="basics"></a>基本知識

基本概念是 Azure 入口網站剖析檔案時所產生的第一個步驟。 除了顯示 `basics` 中指定的元素之外，入口網站會插入元素，以供使用者選擇部署的訂用帳戶、資源群組和位置。 可能的話, 查詢整個部署的參數 (例如叢集名稱或系統管理員認證) 的元素應該會在此步驟中執行。

如果專案的行為取決於使用者的訂用帳戶、資源群組或位置, 則該元素不能用在基本概念中。 例如，**Microsoft.Compute.SizeSelector** 取決於使用者的訂用帳戶和位置，來判斷可用大小的清單。 因此，**Microsoft.Compute.SizeSelector** 只能用於步驟中。 一般而言，basics 中只能使用 **Microsoft.Common** 命名空間中的元素。 雖然其他命名空間中的某些專案 (例如**Microsoft. Compute. 認證**) 仍然是允許的, 但並不依存于使用者的內容。

## <a name="steps"></a>步驟

steps 屬性可以包含零個或多個要在 basics 之後顯示的額外步驟，其中都各包含一個或多個元素。 請考慮針對每個角色或要進行部署的應用程式層新增步驟。 例如, 新增主要節點輸入的步驟, 以及叢集中背景工作節點的步驟。

## <a name="outputs"></a>outputs

Azure 入口網站會使用 `outputs` 屬性，將 `basics` 和 `steps` 的屬性對應至 Azure Resource Manager 部署範本的參數。 這個字典的金鑰是範本參數的名稱，而值則是所參照元素的輸出物件之屬性。

若要設定受控應用程式資源名稱，您必須在輸出屬性中包含名為 `applicationResourceName` 的值。 如果您未設定此值, 應用程式會指派名稱的 GUID。 您可以在使用者介面中包含文字方塊，向使用者要求名稱。

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>資源類型

若要將可用的位置篩選為僅支援要部署之資源類型的位置, 請提供資源類型的陣列。 如果您提供一個以上的資源類型, 則只會傳回支援所有資源類型的位置。 這是選用屬性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Functions

CreateUiDefinition 提供[函式](create-uidefinition-functions.md)來處理元素的輸入和輸出, 以及條件之類的功能。 這些函式在 Azure Resource Manager 範本函式的語法和功能上都很類似。

## <a name="next-steps"></a>後續步驟

createUiDefinition.json 檔案本身有簡單的結構描述。 它的實際深度來自於所有支援的元素和函式。 這些項目會在以下位置更詳細地描述：

- [元素](create-uidefinition-elements.md)
- [函式](create-uidefinition-functions.md)

下列位置會提供 createUiDefinition 的目前 JSON 結構描述： https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json 。

如需使用者介面檔案範例，請參閱 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json)。
