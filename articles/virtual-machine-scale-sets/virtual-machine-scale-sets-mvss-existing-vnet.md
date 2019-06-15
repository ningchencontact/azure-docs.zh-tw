---
title: 在 Azure 擴展集範本中參考現有的虛擬網路 | Microsoft Docs
description: 了解如何將虛擬網路新增到現有的「Azure 虛擬機器擴展集」範本
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8b75b9898eb767866c0843594a82570cfb65d122
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868960"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>在 Azure 擴展集範本中新增對現有虛擬網路的參考

這篇文章說明如何修改[基本的擴展集範本](virtual-machine-scale-sets-mvss-start.md)部署到現有的虛擬網路，而不是建立一個新。

## <a name="change-the-template-definition"></a>變更範本定義

在 [前一篇文章](virtual-machine-scale-sets-mvss-start.md)我們建立基本的擴展集範本。 我們現在會使用較早的範本，並修改它來建立範本來部署擴展集到現有的虛擬網路。 

首先，新增 `subnetId` 參數。 這個字串會傳遞至擴展集組態，讓擴展集可識別要將虛擬機器部署到的預建子網路。 這個字串必須是格式的： `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

例如，若要將擴展集部署到名稱為 `myvnet`、子網路為 `mysubnet`、資源群組為 `myrg` 和訂用帳戶為 `00000000-0000-0000-0000-000000000000` 的現有虛擬網路，subnetId 會是：`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`。

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

接下來，從 `resources` 陣列刪除虛擬網路資源，因為您使用現有虛擬網路，因此不需要部署新的資源。

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

虛擬網路在部署範本之前就已存在，因此不需要指定從擴展集到虛擬網路的 dependsOn 子句。 刪除下列幾行：

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

最後，傳入`subnetId`使用者所設定的參數 (而不是使用`resourceId`若要取得相同部署中的 vnet 識別碼，這是基本的可行擴展集範本的作法)。

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>後續步驟

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
