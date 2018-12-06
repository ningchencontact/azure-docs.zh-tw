---
title: 升級 Azure Service Fabric 叢集的組態 | Microsoft Docs
description: 了解如何使用 Resource Manager 範本，升級可在 Azure 中執行 Service Fabric 叢集的組態。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 9323b393edb808f3d2d069f868deb0b67cd0c871
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2018
ms.locfileid: "51857883"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>在 Azure 中升級叢集的組態 

此文章說明如何自訂 Service Fabric 叢集的各種網狀架構設定。 針對裝載於 Azure 中的叢集，您可以透過 [Azure 入口網站](https://portal.azure.com)或使用 Azure Resource Manager 範本來自訂設定。

> [!NOTE]
> 並非所有設定都可以在入口網站中使用。 若下列設定無法透過入口網站使用，請使用 Azure Resource Manager 範本自訂它。> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>使用 Resource Manager 範本自訂叢集設定
透過 JSON Resource Manager 範本可以設定 Azure 叢集。 若要深入了解不同的設定，請參閱[叢集的組態設定](service-fabric-cluster-fabric-settings.md)。 舉例來說，下列步驟示範如何使用 Azure 資源總管將新設定 *MaxDiskQuotaInMB* 新增至 [Diagnostics] 區段。

1. 移至 https://resources.azure.com。
2. 瀏覽至您的訂用帳戶，方法為展開 [訂用帳戶] -> **\<您的訂用帳戶>** -> **resourceGroups** -> **\<您的資源群組>** -> [提供者] -> **Microsoft.ServiceFabric** -> [叢集] -> **\<您的叢集名稱>**
3. 選取右上角的 [讀取/寫入]。
4. 選取 [編輯]，並更新 `fabricSettings` JSON 元素，然後新增元素：

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

您也可以使用 Azure Resource Manager 透過下列其中一種方式自訂叢集設定：

- 使用 [Azure 入口網站](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template)來匯出並更新 Resource Manger 範本。
- 使用 [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) 來匯出並更新 Resource Manger 範本。
- 使用 [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) 來匯出並更新 Resource Manger 範本。
- 使用 Azure RM PowerShell [Set-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) 和 [Remove-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) 命令來直接修改設定。
- 使用 Azure CLI [az sf cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) 命令來直接修改設定。

## <a name="next-steps"></a>後續步驟
* 深入了解 [Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)。
* 了解如何[相應放大和相應縮小叢集](service-fabric-cluster-scale-up-down.md)。
