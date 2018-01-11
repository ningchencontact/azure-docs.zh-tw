---
title: "轉換 Azure Resource Manager 擴展集範本以使用受控磁碟 | Microsoft Docs"
description: "轉換擴展集範本至受控磁碟擴展集範本。"
keywords: "虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: negat
ms.openlocfilehash: 760e30f5c6f4ecaff299bae1725548a6a7c5184c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>轉換擴展集範本至受控磁碟擴展集範本

使用 Resource Manager 範本來建立不使用受控磁碟之擴展集的客戶可能希望修改它以使用受控磁碟。 本文示範如何使用受管理的磁碟，做為範例使用提取要求從[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)，範例資源管理員範本的社群導向的儲存機制。 您可以在 [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998) 找到完整提取要求，並在下面找到主要差異部分以及說明：

## <a name="making-the-os-disks-managed"></a>將 OS 磁碟設定為受控磁碟

在下列的差異，會移除儲存體帳戶和磁碟的屬性與相關的數個變數。 儲存體帳戶類型也不再需要 （Standard_LRS 是預設值），但您可以視需要指定它。 針對受控磁碟，只支援 Standard_LRS 與 Premium_LRS。 我們在舊範本中使用新的儲存體帳戶尾碼、唯一字串陣列與 SA 計數來產生儲存體帳戶名稱。 這些變數在新範本中已不再是必要項目，因為受控磁碟會代表客戶自動建立儲存體帳戶。 同樣地，vhd 容器名稱和作業系統磁碟名稱就不再需要因為受管理的磁碟會自動命名的基礎儲存體 blob 容器和磁碟。

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


在下列的差異，您可以計算 API 已更新為版本 2016年-04-30-預覽，這是最早的小數位數設定的受管理的磁碟支援必要的版本。 如有需要，您可以使用未受管理的磁碟在新的應用程式開發介面版本與舊語法。 如果您只更新計算應用程式開發介面版本，而且不會變更任何其他項目，範本應該繼續如常運作。

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

下列差異，在儲存體帳戶資源是從資源陣列完全移除。 不再需要資源，因為受管理的磁碟會自動建立它們。

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

在下列的差異，我們可以看到我們會移除 on 子句中參照小數位數設定為已建立儲存體帳戶的迴圈，而定。 在舊範本中，這可以確保會在開始建立擴展集之前先建立儲存體帳戶，但搭配受控磁碟使用時，此子句已非必要。 Vhd 容器屬性也會移除，以及 OS 磁碟名稱屬性，因為這些屬性會自動由實際上受管理的磁碟。 您可以加入`"managedDisk": { "storageAccountType": "Premium_LRS" }`"osDisk 」 組態，如果您想高階 OS 磁碟中。 只有 VM SKU 中具有大寫或小寫 's' 的 VM 可以使用進階磁碟。

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

擴展集設定中並沒有明確的屬性可決定要使用受控或非受控磁碟。 擴展集會根據儲存體設定檔中的屬性來判斷要使用的磁碟。 因此，修改範本以確保擴展集的儲存體設定檔中有正確的屬性非常重要。


## <a name="data-disks"></a>資料磁碟

根據上面的變更，擴展集會針對 OS 磁碟使用受控磁碟，但對於資料磁碟呢？ 若要新增資料磁碟，請在與 "osDisk" 相同層級的 "storageProfile" 下新增 "dataDisks" 屬性。 屬性的值是 JSON 物件清單，其中每個物件都具有屬性 "lun" (對於 VM 上的每個資料磁碟，這必須是唯一的)、"createOption" ("empty" 是目前唯一支援的選項) 與 "diskSizeGB" (以 GB 為單位的磁碟大小，必須大於 0 並小於 1024)，如下列範例中所示： 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

若在此陣列中指定 `n` 磁碟，擴展集中的每個 VM 都會取得 `n` 資料磁碟。 但是請注意，這些資料磁碟是未經處理的裝置。 它們並未格式化。 這是由客戶連結、 分割並格式化磁碟，然後才使用。 （選擇性） 您也可以指定`"managedDisk": { "storageAccountType": "Premium_LRS" }`中指定它應該是高階資料磁碟的每個資料磁碟物件。 只有 VM SKU 中具有大寫或小寫 's' 的 VM 可以使用進階磁碟。

若要深入了解如何搭配擴展集使用資料磁碟，請參閱[此文章](./virtual-machine-scale-sets-attached-disks.md)。


## <a name="next-steps"></a>後續步驟
如需使用擴展集的範例 Resource Manager 範本，請在 [Azure 快速入門範本 github 儲存機制](https://github.com/Azure/azure-quickstart-templates)中搜尋 "vmss"。

如需一般資訊，請參閱 [擴展集的主要登陸頁面](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。

