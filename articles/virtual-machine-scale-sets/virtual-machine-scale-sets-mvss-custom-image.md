---
title: "在 Azure 擴展集範本中參照自訂映像 | Microsoft Docs"
description: "了解如何將自訂映像新增到現有的「Azure 虛擬機器擴展集」範本"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2017
ms.author: negat
ms.openlocfilehash: 28d2c080048a7f82e83ad9c1794c9757b330a8c7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>新增自訂映像至 Azure 擴展集範本

本文說明如何修改[最基本的可行擴展集範本](./virtual-machine-scale-sets-mvss-start.md)從自訂映像部署。

## <a name="change-the-template-definition"></a>變更範本定義

可行的最小小數位數組範本可以看到[這裡](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)，而且在範本部署小數位數設定從自訂映像可以看到[這裡](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json)。 讓我們逐步檢查用來建立此範本 (`git diff minimum-viable-scale-set custom-image`) 的差異：

### <a name="creating-a-managed-disk-image"></a>建立受控磁碟映像

如果您已有自訂的受控磁碟映像 (類型為 `Microsoft.Compute/images` 的資源)，則可略過此節。

首先，新增`sourceImageVhdUri`參數，其為一般化，其中包含自訂映像，從 Azure 儲存體中 blob 的 URI。


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

接下來，加入類型的資源`Microsoft.Compute/images`，這是受管理的磁碟映像一般化 blob 位於 URI 為基礎`sourceImageVhdUri`。 此映像必須位在與使用它的擴展集相同的區域中。 在映像的內容中，指定作業系統類型，而 blob 的位置 (從`sourceImageVhdUri`參數)，和儲存體帳戶類型：

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

在標尺設定資源、 加入`dependsOn`小數位數設定嘗試從該映像進行部署之前，便會建立指向自訂映像，以確定映像的子句：

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>變更擴展集屬性以使用受控磁碟映像

在`imageReference`標尺設定`storageProfile`，而不是指定 「 發行者 」，提供，sku，而且平台映像的版本指定`id`的`Microsoft.Compute/images`資源：

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

在此範例中，使用`resourceId`函式可取得相同的範本中建立的映像的資源識別碼。 如果您事先建立之受管理的磁碟映像，您應該改為提供該映像的識別碼。 這個識別碼的格式必須是： `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`。


## <a name="next-steps"></a>後續步驟

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
