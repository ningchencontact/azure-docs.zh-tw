---
title: "將監視和診斷新增到 Azure 虛擬機器 | Microsoft Docs"
description: "使用 Azure Resource Manager 範本來建立新的 Windows 虛擬機器具有 Azure 診斷擴充功能。"
services: virtual-machines-windows
documentationcenter: 
author: sbtron
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e3ea1687e7fb6cc7af00e03b85fb48b0d7911275
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>使用 Windows VM 和 Azure Resource Manager 範本的監視和診斷
Azure 診斷擴充功能會在以 Windows 為基礎的 Azure 虛擬機器上提供監視和診斷功能。 您可以啟用虛擬機器上的這些功能包括副檔名，做為 Azure Resource Manager 範本的一部分。 請參閱 [使用 VM 延伸模組編寫 Azure 資源管理員範本](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) ，以取得將任何延伸模組納入為虛擬機器範本一部分的詳細資訊。 本文描述如何將 Azure 診斷延伸模組新增至 Windows 虛擬機器範本。  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>將 Azure 診斷延伸模組新增至 VM 資源定義
若要啟用診斷延伸模組在 Windows 虛擬機器，您需要將延伸新增為資源管理員範本中的 VM 資源。

針對簡單以資源管理員為基礎的虛擬機器，將延伸模組組態新增至虛擬機器的「資源」  陣列： 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

另一個常見慣例是在根資源節點，而不是定義在虛擬機器的資源 節點下的範本加入擴充功能組態。 使用此方法時，您必須明確指定延伸模組和虛擬機器之間的階層式關聯性*名稱*和*類型*值。 例如︰ 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

延伸模組一律與虛擬機器相關聯，您可以直接在虛擬機器的資源節點底下定義，或是在基底層級定義並且使用階層式命名慣例，將其與虛擬機器產生關聯。

對於虛擬機器擴展集，延伸模組組態是在 *VirtualMachineProfile* 的 *extensionProfile* 屬性中指定。

*publisher* 屬性具有值 **Microsoft.Azure.Diagnostics**，*type* 屬性具有值 **IaaSDiagnostics**，唯一識別 Azure 診斷延伸模組。

*name* 屬性的值可用來參考資源群組中的延伸模組。 專為將其設定**Microsoft.Insights.VMDiagnosticsSettings** ，可讓使用者輕易識別，確保，監視圖顯示組成正確地在 Azure 入口網站的 Azure 入口網站。

*TypeHandlerVersion* 會指定您想要使用的延伸模組的版本。 設定*autoUpgradeMinorVersion*次要版本**true**可確保您取得最新的次要版本所提供的延伸模組。 強烈建議您一律將 *autoUpgradeMinorVersion* 設為永遠為 **true** ，讓您永遠可以使用具有所有新功能和錯誤修正的可用最新診斷延伸模組。 

*settings* 元素包含延伸模組的組態屬性，可以從延伸模組 (有時稱為公用組態) 設定和讀取。 *Xmlcfg*屬性包含 xml 為基礎的診斷記錄檔中，組態會收集的診斷代理程式的效能計數器等等。 如需 xml 結構描述本身的詳細資訊，請參閱 [診斷組態結構描述](https://msdn.microsoft.com/library/azure/dn782207.aspx) 。 常見的作法是將實際的 xml 組態儲存為 Azure 資源管理員範本中的變數，然後再進行串連和 base64 編碼，以設定 *xmlcfg*的值。 請參閱 [診斷組態變數](#diagnostics-configuration-variables) 以深入了解如何在變數中儲存 xml。 *StorageAccount*屬性會指定哪一個診斷資料傳輸資料的儲存體帳戶名稱。 

*protectedSettings* (有時稱為私用組態) 中的屬性可以設定，但是無法在設定之後讀取。 唯寫性質*protectedSettings*方便來儲存機密資料，例如儲存體帳戶金鑰寫入診斷資料。    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>將診斷儲存體帳戶指定為參數
診斷延伸模組 json 片段上述假設兩個參數*existingdiagnosticsStorageAccountName*和*existingdiagnosticsStorageResourceGroup*指定診斷儲存體儲存診斷資料的帳戶。 當做參數會讓您輕鬆地變更不同環境下的診斷儲存體帳戶，請指定診斷儲存體帳戶，例如您可能想要使用不同的診斷儲存體帳戶進行測試和不同的另一個用於您生產環境部署。  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }        
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
      }
}
```

最佳做法是在不同於虛擬機器資源群組的其他資源群組中指定診斷儲存體帳戶。 資源群組可以視為是具有自己的存留期的部署單位，可以部署虛擬機器以及在新組態更新時重新部署，但是您可能想要跨這些虛擬機器部署繼續在相同的儲存體帳戶中儲存診斷資料。 在不同的資源中擁有儲存體帳戶可讓儲存體帳戶接受來自各種虛擬機器部署的資料，方便疑難排解各種版本之間的問題。

> [!NOTE]
> 如果您從 Visual Studio 建立 windows 虛擬機器範本，可能會使用相同的儲存體帳戶在上傳虛擬機器 VHD 設定預設儲存體帳戶。 這是為了簡化 VM 的初始設定。 若要使用不同的儲存體帳戶可以當做參數傳入的範本重新考量因素。 
> 
> 

## <a name="diagnostics-configuration-variables"></a>診斷組態變數
上述的診斷延伸模組 json 片段會定義 *accountid* 變數，以簡化取得診斷儲存體的儲存體帳戶金鑰：   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

診斷延伸模組的 *xmlcfg* 屬性是使用串連在一起的多個變數進行定義。 這些變數值的格式為 xml，因此必須在設定 json 變數時正確逸出。

以下說明診斷組態 xml，它會收集標準系統層級效能計數器以及一些 Windows 事件記錄檔和診斷基礎結構記錄檔。 已正確逸出和格式化，因此組態可以直接貼到您的範本的變數區段。 請參閱 [診斷組態結構描述](https://msdn.microsoft.com/library/azure/dn782207.aspx) 以取得人類可讀取的組態 xml 的範例。

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

上述的設定中的度量定義 xml 節點是重要的設定項目，因為它會定義效能計數器稍早在 xml 中的定義方式*PerformanceCounter*節點經過彙總和儲存。 

> [!IMPORTANT]
> 這些計量控制了 Azure 入口網站中的監視圖表與警示 。  若要在 Azure 入口網站中查看 VM 的監視資料，必須將具有 *resourceID* 和 **MetricAggregation** 的**計量**節點包含在您的 VM 的診斷組態中。 
> 
> 

以下是度量定義的 xml 的範本： 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

*resourceID* 屬性會唯一識別您的訂用帳戶中的虛擬機器。 請確定使用 subscription() 和 resourceGroup() 函式，這樣範本就會根據訂用帳戶和您要部署的資源群組自動更新這些值。

如果您要建立多個虛擬機器在迴圈中，您必須填入*resourceID*搭配正確區分每個個別 VM copyIndex() 函式的值。 *xmlCfg* 值可以更新為支援此功能，如下所示：  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

*PT1H* 及 *PT1M* 的 MetricAggregation 值表示超過一分鐘的彙總及超過一小時的彙總。

## <a name="wadmetrics-tables-in-storage"></a>儲存體中的 WADMetrics 資料表
上述的度量設定會在下列命名慣例診斷儲存體帳戶中產生資料表：

* **WADMetrics**： 標準首碼的所有 WADMetrics 資料表
* **PT1H**或**PT1M**： 表示此資料表包含超過 1 小時或 1 分鐘的彙總資料
* **P10D**： 表示資料表會包含從資料表開始收集資料的 10 天的資料
* **V2S**： 字串常數
* **yyyymmdd**： 資料表開始收集資料的日期

範例： *WADMetricsPT1HP10DV2S20151108* 將包含從 2015 年 11 月 11 日開始 10 天內，所有超過一小時的彙總計量資料    

每個 WADMetrics 資料表將會包含下列資料行：

* **PartitionKey**： 資料分割索引鍵根據建構*resourceID*來唯一識別 VM 資源的值。 例如： 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey**： 遵循格式`<Descending time tick>:<Performance Counter Name>`。 遞減的時間刻度計算是最大時間刻度減去開始彙總期間時間。 例如，如果於 2015 年 11 月 10 日啟動取樣期間 00:00Hrs UTC，則計算就是： `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`。 記憶體可用位元組數的效能計數器資料列索引鍵看起來像：`2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**： 效能計數器的名稱。 這符合 xml 設定中定義的 *counterSpecifier* 。
* **最大**： 彙總期間的效能計數器的最大值。
* **最小**： 彙總期間的效能計數器的最小值。
* **總**： 彙總期間所報告的效能計數器的所有值的總和。
* **計數**： 效能計數器報告的值總數。
* **平均**： 彙總期間的效能計數器的平均值 （總計/計數） 值。

## <a name="next-steps"></a>後續步驟
* 對於 Windows 虛擬機器具有診斷擴充功能完整的範例範本，請參閱[201-vm-監視-診斷-擴充功能](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* 部署 Azure Resource Manager 範本使用[Azure PowerShell](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或[Azure 命令列](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* 深入了解 [編寫 Azure 資源管理員範本](../../resource-group-authoring-templates.md)
