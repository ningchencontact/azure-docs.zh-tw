---
title: Azure Service Fabric Mesh 的常見問題 | Microsoft Docs
description: 了解 Azure Service Fabric Mesh 的常見問題和解答。
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 12/12/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 27cf4d31f11eaf861d1cafc093d912aa15c8bec0
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979746"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Service Fabric Mesh 的常見問題

Azure Service Fabric Mesh 是一個受到完整管理的服務，讓開發人員能夠部署微服務應用程式，而不需管理虛擬機器、儲存體或網路功能。 本文提供常見問題的解答。

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>如何回報問題或詢問問題？

在 [service-fabric-mesh-preview GitHub 存放庫](https://aka.ms/sfmeshissues) \(英文\) 中提出問題、獲得 Microsoft 工程師所提供的解答，以及回報問題。

## <a name="quota-and-cost"></a>配額和成本

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>參與預覽版的成本為何？

將應用程式或容器部署至 Mesh 預覽版目前不需任何費用。 不過，除非您要積極測試所部署的資源，否則建議您刪除這些資源，而不要讓它們繼續執行。

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>核心和 RAM 數目是否有配額限制？

是。 每個訂用帳戶的配額如下：

- 應用程式數目：5
- 每個應用程式的核心數目：12
- 每個應用程式的 RAM 總計：48 GB
- 網路與輸入端點數目：5
- 您可以連結的 Azure 磁碟區數目：10
- 服務複本數目：3
- 您可以部署的最大容器限制為 4 個核心和 16 GB 的 RAM。
- 您可以將部分核心配置給容器，遞增量為 0.5 個核心，最多 6 個核心。

### <a name="how-long-can-i-leave-my-application-deployed"></a>我可以讓應用程式維持部署狀態幾天？

我們目前將應用程式的存留期限制為兩天。 這是為了充分運用配置給預覽版的可用核心。 因此，只允許您持續執行指定的部署 48 小時，之後將會關閉該部署。

如果確實發生此情況，可以藉由在 Azure CLI 中執行 `az mesh app show` 命令，以驗證系統是否將它關閉。 請檢查是否傳回 `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

例如︰ 

```cli
~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

若要刪除資源群組，請使用 `az group delete <nameOfResourceGroup>` 命令。

## <a name="supported-container-os-images"></a>支援的容器 OS 映像

如果您是在 Windows Fall Creators Update (1709 版) 機器上進行開發，則只能使用 Windows 1709 版 Docker 映像。

如果您是在 Windows 10 April 2018 update (1803 版) 機器上進行開發，則可以使用 Windows 1709 版或 Windows 1803 版的 Docker 映像。

可以使用下列容器 OS 映像部署服務：

- Windows：windowsservercore 和 nanoserver
    - Windows Server 1709 版
    - Windows Server 1803 版
-  Linux
    - 沒有已知的限制

## <a name="developer-experience-issues"></a>開發人員體驗問題

### <a name="dns-resolution-from-a-container-doesnt-work"></a>來自容器的 DNS 解析無法運作

從容器連出到 Service Fabric DNS 服務的 DNS 查詢在某些情況下可能會失敗。 我們正在調查此問題。 減輕問題：

- 使用 Windows Fall Creators Update (1709版) 或更新版本作為基礎容器映像。
- 如果只有服務名稱時無法運作，請嘗試完整名稱：ServiceName.ApplicationName。
- 在您服務的 Docker 檔案中新增 `EXPOSE <port>`，其中的 port 是您公開服務的連接埠。 例如︰

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS 的運作方式和在 Service Fabric 開發叢集與 Mesh 中不一樣

您可能需要在您的本機開發叢集中以及 Azure Mesh 中以不同方式參考服務。

在您的本機開發叢集中，請使用 `{serviceName}.{applicationName}`。 在 Azure Service Fabric Mesh 中，請使用 `{servicename}`。 

Azure Mesh 目前不支援跨應用程式的 DNS 解析。

對於在 Windows 10 上執行 Service Fabric 開發叢集的其他已知 DNS 問題，請參閱：[對 Windows 容器進行偵錯](/azure/service-fabric/service-fabric-how-to-debug-windows-containers)和[已知 DNS 問題](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues)。

### <a name="networking"></a>網路功能

在本機電腦上執行應用程式時，ServiceFabric 網路 NAT 可能會消失。 若要診斷是否發生此問題，請從命令提示字元執行以下命令：

`docker network ls` 並記下是否列出 `servicefabric_nat`。  如果沒有，請執行以下命令：`docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

即使應用程式已經在本機部署且狀況不良，這樣做也可以解決此問題。

### <a name="issues-running-multiple-apps"></a>執行多個應用程式的問題

您可能會遇到跨所有應用程式修正 CPU 可用性與限制。 減輕問題：
- 建立一個五節點叢集。
- 跨已部署的所有應用程式，降低服務的 CPU 使用量。 例如，在服務的 service.yaml 檔案中，將 `cpu: 1.0` 變更為 `cpu: 0.5`

您無法將多個應用程式部署到一個單一節點叢集。 減輕問題：
- 將多個應用程式部署至本機叢集時，請使用五節點叢集。
- 移除您目前沒有在測試的應用程式。

## <a name="feature-gaps-and-other-known-issues"></a>功能差距和其他已知問題

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>部署應用程式之後，與它相關聯的網路資源沒有 IP 位址

有一個已知問題 IP 位址不會立即變成可用的已知問題。 在幾分鐘內檢查網路資源的狀態，以查看相關聯的 IP 位址。

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>我的應用程式無法存取正確的網路/磁碟區資源

請在您的應用程式模型中，使用網路和磁碟區的完整資源識別碼，才能存取相關聯的資源。 以下是來自快速入門範例的範例：

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>當我相應放大時，所有容器都受到影響，包括正在執行的容器

這是一個錯誤 (bug)，目前正在實作修正程式。

## <a name="next-steps"></a>後續步驟

若要深入了解 Service Fabric Mesh，請閱讀[概觀](service-fabric-mesh-overview.md)。
