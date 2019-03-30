---
title: 升級獨立 Azure Service Fabric 叢集的組態 | Microsoft Docs
description: 了解如何升級可執行獨立 Service Fabric 叢集的組態。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: f99c1ebb64bf881bcd42f15e13bb81b96ccfa064
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665596"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>升級獨立叢集的組態 

對於現代化系統來說，升級能力攸關產品是否能長期成功。 Azure Service Fabric 叢集是您擁有的資源。 本文說明如何升級獨立 Service Fabric 叢集的組態設定。

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>在 ClusterConfig.json 檔案中自訂叢集設定
獨立叢集需透過 ClusterConfig.json 檔案來進行設定。 若要深入了解不同的設定，請參閱[獨立 Windows 叢集的組態設定](service-fabric-cluster-manifest.md)。

您可以在 ClusterConfig.json 中 [Cluster properties](./service-fabric-cluster-manifest.md#cluster-properties) 區段底下的 `fabricSettings` 區段中新增、更新或移除設定。 

例如，下列 JSON 會將新設定 *MaxDiskQuotaInMB* 新增至 `fabricSettings` 底下的 [Diagnostics] 區段：

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

在您修改 ClusterConfig.json 檔案中的設定之後，請[測試叢集組態](#test-the-cluster-configuration)，然後[升級叢集組態](#upgrade-the-cluster-configuration)以將設定套用至叢集。 

## <a name="test-the-cluster-configuration"></a>測試叢集組態
起始組態升級之前，您可以執行獨立套件中的 PowerShell 指令碼來對新叢集組態 JSON 進行測試：

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

或是使用此指令碼：

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

某些組態無法升級，例如端點、叢集名稱、節點 IP 等。新的叢集組態 JSON 會對照舊的叢集組態 JSON 來進行測試，如有任何問題，就會在 PowerShell 視窗中擲回錯誤。

## <a name="upgrade-the-cluster-configuration"></a>升級叢集組態
若要升级群集配置，请运行 [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)。 組態升級是按升級網域逐一處理。

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>升級叢集憑證組態
叢集憑證可用於叢集節點之間的驗證。 在進行憑證變換時請格外小心，因為若發生失敗，將會讓叢集節點之間無法通訊。

支援的選項有四個：  

* 单证书升级：升级路径为“证书 A（主证书）-> 证书 B（主证书）-> 证书 C（主证书）->....”

* 双证书升级：升级路径为“证书 A（主证书）-> 证书 A（主证书）和证书 B（辅助证书）-> 证书 B（主证书）-> 证书 B（主证书）和证书 C（辅助证书）-> 证书 C（主证书）-> ...”

* 证书类型升级：基于指纹的证书配置 <-> 基于 CommonName 的证书配置。 例如，憑證指紋 A (主要) 和指紋 B (次要) -> 憑證 CommonName C。

* 证书颁发者指纹升级：升级路径为“Certificate CN=A,IssuerThumbprint=IT1 (Primary) -> Certificate CN=A,IssuerThumbprint=IT1,IT2 (Primary) -> Certificate CN=A,IssuerThumbprint=IT2 (Primary)”。


## <a name="next-steps"></a>後續步驟
* 了解如何自訂一些 [Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)。
* 了解如何[扩大和缩小群集](service-fabric-cluster-scale-up-down.md)。
* 了解[應用程式升級](service-fabric-application-upgrade.md)。

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
