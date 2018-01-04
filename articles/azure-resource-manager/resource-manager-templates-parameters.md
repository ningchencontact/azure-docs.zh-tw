---
title: "Azure 資源管理員範本參數 區段 |Microsoft 文件"
description: "描述使用宣告式 JSON 語法的 Azure 資源管理員範本的參數區段。"
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
ms.date: 12/11/2017
ms.author: tomfitz
ms.openlocfilehash: 7d0f53751bf529d52c156a8b9319b10560eb8997
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Azure Resource Manager 範本的參數 > 一節
在範本的 parameters 區段中，您會指定可在部署資源時輸入的值。 提供針對特定環境 (例如開發、測試和生產環境) 量身訂做的參數值，可讓您自訂部署。 您不必在範本中提供參數，但若沒有參數，您的範本一律會部署具有相同名稱、位置和屬性的相同資源。

您會限制為 255 個範本中的參數。 您可以使用包含多個屬性，這篇文章中所述的物件，以減少參數數目。

## <a name="define-and-use-a-parameter"></a>定義和使用參數

下列範例顯示簡單的參數定義。 它會定義參數的名稱，並指定需要字串值。 參數只接受其預期用途有意義的值。 在部署期間未不提供任何值時，它會指定預設值。 最後，此參數會包含其用途的描述。 

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

在範本中，您可以參考使用下列語法參數的值：

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="available-properties"></a>可用的屬性

上述範例中示範了只對某些參數區段中，您可以使用的屬性。 可用的屬性包括：

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| 元素名稱 | 必要 | 說明 |
|:--- |:--- |:--- |
| parameterName |是 |參數名稱。 必須是有效的 JavaScript 識別碼。 |
| type |是 |參數值類型。 允許的類型和值都是**字串**， **secureString**， **int**， **bool**，**物件**， **secureObject**，和**陣列**。 |
| defaultValue |否 |如果未提供參數值，則會使用參數的預設值。 |
| allowedValues |否 |參數的允許值陣列，確保提供正確的值。 |
| minValue |否 |int 類型參數的最小值，含此值。 |
| maxValue |否 |int 類型參數的最大值，含此值。 |
| minLength |否 |字串、secureString 及陣列類型參數長度的最小值，含此值。 |
| maxLength |否 |字串、secureString 及陣列類型參數長度的最大值，含此值。 |
| description |否 |透過入口網站向使用者顯示的參數說明。 |

## <a name="template-functions-with-parameters"></a>使用參數的樣板函式

當為參數提供預設值，您可以使用大部分的樣板函式。 您可以使用另一個參數值來建立預設值。 下列範本會示範使用函式中的預設值：

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

您無法使用`reference`函式的參數區段中。 部署之前評估參數而`reference`函式無法取得資源的執行階段狀態。 

## <a name="objects-as-parameters"></a>物件做為參數

它可以您更輕鬆地將它們傳遞做為物件，以組織相關的值。 這個方法也會減少在範本中的參數數目。

在範本中定義參數，並在部署期間指定的 JSON 物件，而不是單一值。 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

然後，使用點運算子參考參數的子屬性。

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location":"[resourceGroup().location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="recommendations"></a>建議
當您使用參數時，下列資訊可能會很有幫助︰

* 將參數的使用最小化。 可能的話，使用變數或常值。 只針對這些案例使用參數︰
   
   * 您想要根據環境 (SKU、大小、容量) 使用變化的設定。
   * 您希望能方便識別而指定的資源名稱。
   * 您經常用來完成其他工作 (例如，管理使用者名稱) 的值。
   * 機密資料 (例如密碼)。
   * 建立多個資源類型執行個體時要使用的數字或值陣列。
* 參數名稱使用駝峰式大小寫。
* 在中繼資料中提供每個參數的描述：

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* 定義參數的預設值 (密碼和 SSH 金鑰除外)。 藉由提供預設值，參數成為選擇性在部署期間。 預設值可以是空字串。 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* 使用**SecureString**所有密碼和密碼。 如果您在 JSON 物件中傳遞敏感資料，請使用 **secureObject** 類型。 部署資源後，無法讀取類型為 secureString 或 secureObject 的範本參數。 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* 可能的話，請勿使用參數來指定位置。 請改為使用資源群組的 **location** 屬性。 針對所有資源使用 **resourceGroup().location** 運算式，就會將範本中的資源作為資源群組部署在相同的位置：
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   如果只在有限的位置支援某資源類型，您可能想要直接在範本中指定有效的位置。 如果您必須使用 **location** 參數，請儘可能和可能位於相同位置的資源一起共用該參數值。 這個方法會降低的次數，系統會要求使用者提供位置資訊。
* 資源類型的 API 版本要避免使用參數或變數。 資源屬性和值可能會隨版本號碼而不同。 將 API 版本設定為參數或變數時，程式碼編輯器中的 Intellisense 會無法判斷正確的結構描述。 請改為將 API 版本硬式編碼在範本中。
* 請避免符合中的部署命令的參數在範本中指定參數名稱。 資源管理員解析此命名的衝突，加入後置**FromTemplate**與樣板參數。 例如，如果您在範本中包含名為 **ResourceGroupName** 的參數，這會與 [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) Cmdlet 中的 **ResourceGroupName** 參數發生衝突。 部署期間，系統會提示您為 **ResourceGroupNameFromTemplate** 提供值。

## <a name="example-templates"></a>範本的範例

這些範例範本示範如何使用參數的某些情況。 將其部署到測試參數不同的案例中處理的方式。

|範本  |說明  |
|---------|---------|
|[具有預設值的函式的參數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | 示範如何定義參數的預設值時，請使用樣板函式。 範本不會部署任何資源。 它會建構參數值，並傳回這些值。 |
|[參數物件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | 示範如何使用參數的物件。 範本不會部署任何資源。 它會建構參數值，並傳回這些值。 |

## <a name="next-steps"></a>後續步驟

* 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
* 如需如何在部署期間輸入參數值的資訊，請參閱 [使用 Azure Resource Manager 範本部署資源](resource-group-template-deploy.md)。 
* 如需您可以在範本內使用哪些函式的詳細資料，請參閱 [Azure Resource Manager 範本函式](resource-group-template-functions.md)。
* 使用參數物件的相關資訊，請參閱[物件做為 Azure Resource Manager 範本中的參數](/azure/architecture/building-blocks/extending-templates/objects-as-parameters)。