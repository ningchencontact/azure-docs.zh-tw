---
title: "Azure Resource Manager 範本輸出 |Microsoft 文件"
description: "描述如何定義使用宣告式 JSON 語法 Azure Resource Manager 範本輸出。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: 485a3eb5c5d04d1540482245d088c48645704465
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2017
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中輸出區段
在輸出區段中，您可以指定從部署傳回的值。 例如，您可以傳回 URI 以存取所部署的資源。

## <a name="define-and-use-output-values"></a>定義和使用輸出值

下列範例會示範如何傳回公用 IP 位址的資源識別碼：

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

部署之後，您可以擷取與指令碼的值。 對於 PowerShell，請使用：

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

對於 Azure CLI，請使用：

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

您也可以使用連結的範本從擷取的輸出值[參考](resource-group-template-functions-resource.md#reference)函式。 若要從連結的範本取得輸出值，請使用如下語法擷取屬性值：`"[reference('<name-of-deployment>').outputs.<property-name>.value]"`。

比方說，您可以從連結的範本中擷取值，負載平衡器上設定的 IP 位址。

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

## <a name="available-properties"></a>可用的屬性

下列範例顯示輸出定義的結構：

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| 元素名稱 | 必要 | 說明 |
|:--- |:--- |:--- |
| outputName |是 |輸出值的名稱。 必須是有效的 JavaScript 識別碼。 |
| type |是 |輸出值的類型。 輸出值支援與範本輸入參數相同的類型。 |
| value |是 |評估並傳回做為輸出值的範本語言運算式。 |

## <a name="recommendations"></a>建議

如果您使用範本建立的公用 IP 位址，包括可傳回的 IP 位址和完整的網域名稱 (FQDN) 的詳細資料的輸出區段。 您可以使用輸出值，輕鬆在部署後擷取公用 IP 位址和 FQDN 的相關詳細資料。

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>範本的範例


|範本  |說明  |
|---------|---------|
|[複製變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 建立複雜的變數，並輸出這些值。 不會部署任何資源。 |
|[公用 IP 位址](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 建立公用 IP 位址，並輸出的資源 id。 |
|[負載平衡器](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 上述範本的連結。 建立負載平衡器時，會在輸出中使用的資源識別碼。 |


## <a name="next-steps"></a>後續步驟
* 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
* 如需您可以在範本內使用哪些函式的詳細資料，請參閱 [Azure Resource Manager 範本函式](resource-group-template-functions.md)。
* 若要在部署期間合併多個範本，請參閱 [透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。
* 您可能需要使用不同資源群組內的資源。 這案例常見於使用多個資源群組之間所共用的儲存體帳戶或虛擬網路時。 如需詳細資訊，請參閱 [resourceId 函式](resource-group-template-functions-resource.md#resourceid)。