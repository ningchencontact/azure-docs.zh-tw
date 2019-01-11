---
title: 如何建立 Azure Resource Manager 範本
description: 說明撰寫 Azure Resource Manager 範本的程序。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: abfc7ce78e8676e9560621be1ec9a81717d958e5
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994278"
---
# <a name="create-azure-resource-manager-template"></a>建立 Azure Resource Manager 範本

本文將說明建立 Azure Resource Manager 範本時所需的程序和決策。 其中提供的範例和功能概觀可協助您撰寫範本。 本文會假設您要將資源部署到資源群組。 如果您要將資源部署到 Azure 訂用帳戶，例如建立 Azure 原則或角色型存取控制指派，請參閱[為 Azure 訂用帳戶建立資源群組與資源](deploy-to-subscription.md)。

## <a name="select-json-editor"></a>選取 JSON 編輯器

Resource Manager 範本是 JSON 檔案。 您需要良好的撰寫工具來編輯 JSON 檔案。 您有許多選擇，但如果您還沒有慣用的編輯器，請安裝 [Visual Studio Code (VS Code)](https://code.visualstudio.com/)。 

安裝 VS Code 之後，請新增 [Azure Resource Manager 工具擴充功能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)。 此擴充功能會新增許多可簡化範本撰寫的功能。

![Visual Studio Code 範本](./media/how-to-create-template/template-visual-studio-code.png)

螢幕擷取畫面顯示已在 Visual Studio Code 中開啟的 Resource Manager 範本。 

如需有關安裝 Resource Manager 工具擴充功能及如何使用 VS Code 的教學課程，請參閱[快速入門：使用 Visual Studio Code 建立 Azure Resource Manager 範本](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)。

## <a name="understand-the-template-structure"></a>了解範本結構

我們將藉由檢閱範本的各部分，來了解範本的運作方式。 您的範本可能沒有每一個區段。 您應著重的區段為：

* [參數](resource-manager-templates-parameters.md)區段，顯示在部署期間用來自訂所部署基礎結構的可指定值。 

* [變數](resource-manager-templates-variables.md)區段，顯示用在整個範本上的值。

* [函式](resource-group-authoring-templates.md#functions)區段，顯示範本中使用的自訂範本運算式。

* [資源](resource-manager-templates-resources.md)區段，顯示部署到您訂用帳戶的 Azure 資源。

* [輸出](resource-manager-templates-outputs.md)區段，顯示完成部署之後所傳回的值。

## <a name="look-for-similar-templates"></a>尋找相似的範本

通常，您可以找到其中所部署解決方案與您所需類似的現有範本。 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)有數百個社群投稿的範本。

![範本快速入門存放庫](./media/how-to-create-template/template-quickstart-repo.png)

搜尋該存放庫，以找出類似您需求的範本。 如果範本沒有完全符合您的需求也沒關係，您可以自訂該範本。

找到範本之後，選取 [瀏覽 Github]，然後從存放庫複製 **azuredeploy.json** 檔案。 在 VS Code 中，建立名為 **azuredeploy.json** 的新檔案，然後新增您從快速入門存放庫複製的範本檔案內容。

## <a name="add-resources"></a>新增資源

您可能會想要自訂範本，以確定其完全符合您的需求。 首先，查看已部署的資源。 您可能需要新增、移除或變更範本中的資源。 如需資源的說明和語法，請參閱 [Azure Resource Manager 範本參考](/azure/templates/)。

![範本參考](./media/how-to-create-template/template-reference.png)

檢閱那些資源的屬性之後，即可進行任何必要的變更。 如需有關如何定義資源的建議，請參閱[資源 - 建議做法](template-best-practices.md#resources)。

## <a name="add-or-remove-parameters"></a>新增或移除參數

您也可以調整您範本的參數。 您可以根據部署期間想達成的自訂比例來新增或移除參數。 如需有關如何使用參數的建議，請參閱[參數 - 建議做法](template-best-practices.md#parameters)。

## <a name="add-tags"></a>新增標記

您可以在資源上新增標記，以邏輯方式按照類別來組織資源，並劃分帳務成本。 新增標記很簡單，將其套用在該資源的 JSON 上即可。 例如，下列的儲存體帳戶有兩個標記：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

您也可以從參數中動態地套用標記。 如需詳細資訊，請參閱[範本中的標記](resource-manager-templates-resources.md#tags)。

## <a name="review-template-functions"></a>檢閱範本函式

您可能會注意到範本中以括弧括住的運算式，例如 `"[some-expression]"`。 這些運算式會使用範本函式，以動態方式建構部署期間的值。

例如，您通常會看到如下的運算式：

```json
"name": "[parameters('siteName')]"
```

該運算式會取得參數的值。 該值會指派給名稱屬性。

或者，您可能會看到更複雜的運算式，其使用數個如下所示的函式：

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

該運算式會取得具有儲存體帳戶屬性的物件。

若要了解函式的功能，請檢閱[範本函式參考](resource-group-template-functions.md)文件。

## <a name="create-more-than-one-instance"></a>建立多個執行個體

有時候，您可能會想要建立多個資源執行個體。 例如，您可能需要數個儲存體帳戶。 如果不要透過您的範本重複使用資源，您可以使用 `copy` 語法來指定多個執行個體。

下列範例會建立 3 個儲存體帳戶：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

您也可以從參數動態地指定執行個體數目。 如需詳細資訊，請參閱[在 Azure Resource Manager 範本中部署資源或屬性的多個執行個體](resource-group-create-multiple.md)。

## <a name="conditionally-deploy-a-resource"></a>有條件地部署資源

有時候，您需要在部署期間指定是否要部署範本中的資源。 例如，您想彈性地部署新資源或使用現有資源。 `condition` 元素可讓您開啟或關閉資源的部署。 當 condition 元素中的運算式成立時，即部署資源。 若不成立，就會在部署期間略過該資源。

下列範例顯示有條件地部署儲存體帳戶：

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

如需詳細資訊，請參閱 [condition 元素](resource-manager-templates-resources.md#condition)。

## <a name="review-dependencies"></a>檢閱相依性

在您的範本中，有些資源必須比其他資源先部署。 例如，必須先有 SQL 伺服器，才能建立 SQL 資料庫。 若使用 [reference 函式](resource-group-template-functions-resource.md#reference)，Resource Manager 就會隱含地決定資源的部署順序。 不過，在某些情況下，您需要使用 `dependsOn` 元素來明確定義相依性。 請檢閱您的範本，看看是否需要新增任何相依性。 請勿新增不必要的相依性，因為這些相依性可能會降低部署速度，或建立循環參考。

下圖顯示各種 App Service 資源的相依性順序：

![Web 應用程式相依性](./media/how-to-create-template/web-dependencies.png)

下列範例將說明範本中定義相依性的部分。

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

如需詳細資訊，請參閱[定義 Azure Resource Manager 範本中部署資源的順序](resource-group-define-dependencies.md)。

## <a name="review-recommended-practices"></a>檢閱建議做法

部署您的範本之前，請先檢閱 [Azure Resource Manager 範本最佳做法](template-best-practices.md)，以查看是否有任何可在您範本中實作的建議做法。

如果您需要在不同的 Azure 雲端環境中使用範本，請參閱[針對雲端一致性開發 Azure Resource Manager 範本](templates-cloud-consistency.md)。

## <a name="next-steps"></a>後續步驟

* 若要部署範本時，請參閱[使用 Azure CLI 進行部署](resource-group-template-deploy-cli.md)或[使用 PowerShell 進行部署](resource-group-template-deploy.md)。
* 如需建立範本的逐步快速入門，請參閱[使用 Visual Studio Code 建立 Azure Resource Manager 範本](resource-manager-quickstart-create-templates-use-visual-studio-code.md)。
* 如需在範本中可用函式的清單，請參閱 [範本函式](resource-group-template-functions.md)。
