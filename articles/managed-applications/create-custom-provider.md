---
title: 以 Azure Custom Providers Preview 建立資源提供者
description: 說明如何建立資源提供者並部署其自訂資源類型。
services: managed-applications
author: MSEvanhi
ms.service: managed-applications
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 34dd5efda2c9f6cc9a7b5ddcde06e8f7d27de901
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65157336"
---
# <a name="tutorial-create-custom-provider-and-deploy-custom-resources"></a>教學課程：建立自訂提供者並部署自訂資源

在本教學課程中，您要建立自己的資源提供者，並部署該資源提供者的自訂資源類型。 如需自訂提供者的詳細資訊，請參閱 [Azure Custom Providers Preview 概觀](custom-providers-overview.md)\(英文\)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程中的步驟，您需要呼叫 REST 作業。 [REST 要求有各種不同的傳送方式](/rest/api/azure/)\(英文\)。 如果尚未準備好 REST 作業適用的工具，請安裝 [ARMClient](https://github.com/projectkudu/ARMClient)。 這是一種開放原始碼命令列工具，可簡化 Azure Resource Manager API 的叫用作業。

## <a name="deploy-custom-provider"></a>部署自訂提供者

若要設定自訂提供者，請將[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json)部署至您的 Azure 訂用帳戶。

部署範本後，您的訂用帳戶會具有以下資源：

* 函數應用程式，包含資源和動作所適用的作業。
* 儲存體帳戶，用於儲存透過自訂提供者建立的使用者。
* 自訂提供者，定義了自訂資源的類型與動作， 會使用函數應用程式端點來傳送要求。
* 來自於自訂資源提供者的自訂資源。

若要使用 PowerShell 部署自訂提供者，請使用：

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

或者也可以使用以下按鈕來部署解決方案：

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>檢視自訂提供者與資源

在入口網站中，自訂提供者是隱藏的資源類型。 若要確認資源提供者是否已部署，請瀏覽至資源群組。 選取 [顯示隱藏的類型] 選項。

![顯示隱藏的資源類型](./media/create-custom-providers/show-hidden.png)

若要查看您所部署的自訂資源類型，請在您的資源類型上使用 GET 作業。

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

透過 ARMClient 使用：

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

您會收到回應：

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

## <a name="call-action"></a>呼叫動作

您的自訂提供者也有名為 **ping** 的動作。 處理該要求的程式碼會在函數應用程式中實作。 ping 動作會以問候語回覆。

若要傳送 ping 要求，請在您的自訂提供者上使用 POST 作業。

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

透過 ARMClient 使用：

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

您會收到回應：

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>建立資源類型

若要建立自訂資源類型，您可以在範本中部署該資源。 在本教學課程過程中部署的範本內，有介紹此種方式。 您也可以傳送資源類型 的 PUT 要求。

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

透過 ARMClient 使用：

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

您會收到回應：

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

## <a name="next-steps"></a>後續步驟

如需自訂提供者的簡介，請參閱 [Azure Custom Providers Preview 概觀](custom-providers-overview.md)\(英文\)。
