---
title: 使用 Azure 虛擬機器擴展集進行 OS 映像自動升級 | Microsoft Docs
description: 了解如何在擴展集中的 VM 執行個體上自動升級 OS 映像
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: cf25d08fc9a0e1ae458d350be93af31447928ecb
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069449"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure 虛擬機器擴展集的 OS 映像自動升級

OS 映像自動升級是 Azure 虛擬機器擴展集的一個功能，可將所有 VM 自動升級為最新的 OS 映像。

作業系統自動升級具有下列特性︰

- 一旦設定之後，映像發行者所發行的最新作業系統映像便會自動套用至擴展集，完全不需要使用者介入。
- 每次發行者發行新的平台映像時，都會以循環方式批次升級執行個體。
- 與應用程式健康狀態探查整合。
- 適用於所有 VM 大小，且適用於 Windows 和 Linux 平台映像。
- 您可以隨時選擇不自動升級 (您也可以手動起始作業系統升級)。
- VM 的作業系統磁碟會取代為使用最新映像版本建立的新作業系統磁碟。 系統會執行已設定的延伸模組和自訂資料指令碼，同時保留已保存的資料磁碟。
- 目前不支援 Azure 磁碟加密 (預覽狀態)。  

## <a name="how-does-automatic-os-image-upgrade-work"></a>OS 映像自動升級工作進行的方式為何？

升級的方式是使用以最新版映像建立的 VM 來取代 OS 映像。 系統會執行任何已設定的延伸模組和自訂資料指令碼，同時保留已保存的資料磁碟。 為了將應用程式停機時間降到最低，升級會將機器分成批次進行，每次升級不會超過擴展集的 20%。 您也可以選擇整合 Azure Load Balancer 應用程式健康狀態探查。 強烈建議整合應用程式活動訊號，並在升級程序中驗證每個批次的更新是否都成功。 執行步驟如下： 

1. 在升級程序開始之前，協調器會確保狀況不良的執行個體數量少於 20%。 
2. 識別要升級的批次 VM 執行個體，並使用總執行個體計數的 20% 為批次上限。
3. 升級此批次之 VM 執行個體的 OS 映像。
4. 如果客戶已經設定應用程式健康狀態探查，升級需要等待探查變成健康 (最多 5 分鐘)，才會繼續進行下一批次的更新。 
5. 如果還有要升級的執行個體，請為下一個批次移至步驟 1)，否則就表示升級已完成。

擴展集的 OS 升級協調器在升級每個批次之前，都會先檢查整體的 VM 執行個體健康情況。 升級某個批次的同時，在 Azure 資料中心可能會有其他並行的計劃性或非計劃性維護，這些維護作業可能會影響 VM 的可用性。 因此，超過 20% 的執行個體可能會暫時關閉。 在這種情況下，擴展集升級會在目前批次結束時停止。

## <a name="supported-os-images"></a>支援的作業系統映像
目前僅支援特定的作業系統平台映像。 您目前無法使用您自己建立的自訂映像。 

目前支援下列的 SKU (未來將會新增更多)：
    
| 發行者               | OS 供應項目      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS *        | 
| Rogue Wave (OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | Stable             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |

* 目前正在推出這些映像的支援，且很快就能在所有的 Azure 區域中取得。 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>設定 OS 映像自動升級的需求

- 平台映像的 *version* 屬性必須設為 *latest*。
- 針對非 Service Fabric 擴展集使用應用程式健康狀態探查。
- 請確定所參考的擴展集模型可取得且保持為最新版。 
  在 VM 延伸模組屬性中啟動承載的 Exa.SAS URI、儲存體帳戶中的承載，都參照到模型中的祕密。 

## <a name="configure-automatic-os-image-upgrade"></a>設定 OS 映像自動升級
若要設定 OS 映像自動升級，請確定擴展集模型定義中的 *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* 屬性是設為 *true*。 

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{ 
  "properties": { 
    "upgradePolicy": { 
      "automaticOSUpgradePolicy": { 
        "enableAutomaticOSUpgrade":  true 
      } 
    } 
  } 
} 
```

下列範例會使用 Azure CLI (2.0.47 或更新版本)，為名為 *myResourceGroup* 的資源群組中，稱為 *myVMSS* 的擴展集設定自動升級：

```azurecli
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```
透過 Azure PowerShell 設定此屬性的支援很快就會推出。

## <a name="using-application-health-probes"></a>使用應用程式健康狀態探查 

在作業系統升級期間，擴展集中的 VM 執行個體一次升級一個批次。 只有客戶應用程式在已升級的 VM 執行個體上狀況良好時，升級才應該繼續。 建議應用程式向擴展集的作業系統升級引擎提供健康情況訊號。 根據預設，平台在作業系統升級期間會考慮 VM 電源狀態和延伸模組佈建狀態，以判斷 VM 執行個體在升級之後是否狀況良好。 在 VM 執行個體的作業系統升級期間，VM 執行個體上的作業系統磁碟會根據最新的映像版本，取代為新的磁碟。 作業系統升級完成後，已設定的延伸模組便會在這些 VM 上執行。 只有在 VM 上成功佈建所有擴充功能之後，才會將應用程式視為狀況良好。 

您可以使用應用程式健康情況探查選擇性地設定擴展集，以便為平台提供精確的應用程式持續狀態的相關資訊。 應用程式健康情況探查是當作健康情況訊號使用的自訂負載平衡器探查。 在擴展集 VM 執行個體上執行的應用程式可以回應外部 HTTP 或 TCP 要求，以指出它是否狀況良好。 如需有關自訂負載平衡器探查運作方式的詳細資訊，請參閱[了解負載平衡器探查](../load-balancer/load-balancer-custom-probe-overview.md)。 作業系統自動升級並不需要但強烈建議使用應用程式健康情況探查。

如果擴展集設定為使用多個放置群組，則需要用到使用[標準負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)的探查。

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>將自訂負載平衡器探查設定為擴展集上的應用程式健康情況探查
最佳作法是，針對擴展集健康情況明確地建立負載平衡器探查。 系統可能會針對現有的 HTTP 探查或 TCP 探查使用相同的端點，但健康情況探查可能會需要不同於傳統負載平衡器探查的行為。 例如，如果執行個體的負載太高，傳統負載平衡器探查可能會傳回狀況不良，因而可能不適用於判斷作業系統自動升級期間的執行個體健康情況。 將探查設定為具有不到兩分鐘的高探查率。

您可以在擴展集的 *networkProfile* 中參考負載平衡器探查，而且可以與對內或對外公開的負載平衡器建立關聯，如下所示：

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```
> [!NOTE]
> 搭配 Service Fabric 使用自動 OS 升級時，新的 OS 映像將以更新網域對更新網域的方式推出，以維持在 Service Fabric 中執行之服務的高可用性。 如需 Service Fabric 叢集持久性特性的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)。

### <a name="keep-credentials-up-to-date"></a>保持認證為最新狀態
如果您的擴展集使用任一認證來存取外部資源，例如如果已設定的 VM 延伸模組針對儲存體帳戶使用 SAS 權杖，您必須確定認證是最新狀態。 如果有任何認證 (包括憑證和權杖) 已過期，升級將會失敗，第一批的 VM 將會是失敗狀態。

資源驗證失敗時，復原 VM 並重新啟用自動 OS 升級的建議步驟如下：

* 重新產生傳遞至擴充功能的權杖 (或任何其他認證)。
* 請確認從 VM 內用來向外部實體通訊的任何認證是最新狀態。
* 將擴展集模型中的擴充功能更新為任一新權杖。
* 部署更新的擴展集，這會更新所有 VM 執行個體，包括失敗的 VM 執行個體。 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>取得 OS 映像自動升級的記錄 
您可以使用 Azure PowerShell、Azure CLI 2.0 或 REST API，檢查您擴展集上最近執行之 OS 升級的記錄。 您可以取得過去兩個月內的最近五次 OS 升級嘗試的記錄。

### <a name="azure-powershell"></a>Azure PowerShell
下列範例會使用 Azure PowerShell，為名為 *myResourceGroup* 的資源群組中，稱為 *myVMSS* 的擴展集檢查其狀態：

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
下列範例會使用 Azure CLI (2.0.47 或更新版本)，為名為 *myResourceGroup* 的資源群組中，稱為 *myVMSS* 的擴展集檢查其狀態：

```azurecli
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
下列範例會使用 REST API，為名為 *myResourceGroup* 的資源群組中，稱為 *myVMSS* 的擴展集檢查其狀態：

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
請在這裡參考此 API 的文件： https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory。

GET 呼叫會傳回類似下列範例輸出的內容：

```json
{
  "value": [
    {
      "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
} 
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>如何取得最新版的平台 OS 映像？ 

您可以使用以下範例來取得支援 OS 自動升級之 SKU 的映像版本： 

```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

```azurecli
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>透過範本進行部署

您可以使用下列範本部署使用自動升級的擴展集 <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>自動輪流升級 - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>後續步驟
如需有關如何搭配擴展集使用作業系統自動升級的其他範例，請參閱[預覽功能的 GitHub 儲存機制](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)。
