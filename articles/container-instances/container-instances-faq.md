---
title: Azure 容器執行個體-常見問題集
description: Azure Container Instances 服務的相關常見問題的解答
services: container-instances
author: dkkapur
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 5657ac9f10c42c2201641c9af447b7ad2e6a3507
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079011"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Azure 容器執行個體相關的常見問題集

本文說明 Azure 容器執行個體的相關常見問題的解答。

## <a name="deployment"></a>部署

### <a name="how-large-can-my-container-image-be"></a>大我的容器映像可？

Azure 容器執行個體上部署容器映像的大小上限是 15 GB。 您可以將較大的映像部署目前完全視您部署，但這不保證。

您的容器映像的大小會影響部署，因此一般來說您想要保留您的容器映像越小越好需要多久。

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>如何加速我的容器的部署？

因為部署時間主要強硬的其中一個映像大小，尋找以減少大小的方式。 移除您不需要或縮小映像中的圖層的大小 （挑選較淺的基本 OS 映像） 的圖層。 例如，如果您執行 Linux 容器，請考慮使用 Alpine 為基本映像，而不是完整的 Ubuntu Server。 同樣地，對於 Windows 容器，使用 Nano Server 基本映像的話。 

您也應該檢查在 Azure 容器映像，可透過預先快取映像清單[列出快取映像](/rest/api/container-instances/listcachedimages)API。 您可以切換移出其中一個預先快取的映像的映像層。 

查看更多[詳細指引](container-instances-troubleshooting.md#container-takes-a-long-time-to-start)於降低容器啟動時間。

### <a name="what-windows-base-os-images-are-supported"></a>支援哪些 Windows 基本 OS 映像？

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 的基底映像

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`， `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`，  `10.0.14393.x`

> [!NOTE]
> 不支援以半年通道發行版本 1709年或 1803年基礎的 Windows 映像。

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 和用戶端基底映像 （預覽）

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`， `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`， `1809`， `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`， `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>在我的容器應該使用何種.NET 或.NET Core 的映像層？ 

使用符合您需求的最小映像。 針對 Linux，您可以使用*執行階段 alpine*以來的.NET Core 2.1 版本支援.NET Core 映像。 針對 Windows，如果您使用完整的.NET Framework 中，然後您需要使用 Windows Server Core 映像 (僅限執行階段映像，這類*4.7.2-windowsservercore-ltsc2016*)。 僅執行階段映像會比較小，但並不支援需要.NET SDK 的工作負載。

## <a name="availability-and-quotas"></a>可用性和配額

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>多少個核心和記憶體應該我為配置容器或容器群組？

這其實取決於您的工作負載。 從小規模著手並測試以查看您的容器的執行方式的效能。 [監視 CPU 和記憶體資源使用量](container-instances-monitor.md)，然後新增 核心或根據您在容器中部署處理程序種類的記憶體。 

請確定也會檢查[資源可用性](container-instances-region-availability.md#availability---general)您正在為上限 CPU 核心和記憶體可以使用每個容器群組中部署的區域。 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>在將 ACI 執行在哪些基礎結構？

Azure 容器執行個體的目標是為無伺服器的隨選容器服務，因此我們想要專注於開發您的容器，而不必擔心基礎結構 ！ 對於很好奇，或想要來進行比較，對效能，ACI 上執行的各種不同的 Sku，Azure Vm 的集主要來自 F 和 D 系列。 我們預期這在未來變更，隨著我們持續開發及最佳化服務。 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>我想要部署千個核心上 ACI-我可以取得我的配額增加嗎？
 
是 （有時候）。 請參閱[配額和限制](container-instances-quotas.md)針對目前的配額，並要求可以提高哪些限制。

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>我可以部署超過 4 個核心與 16 GB 的 RAM 嗎？

尚未提供。 目前，這些都是容器群組的最大值。 使用特定的需求或要求，請連絡 Azure 支援。 

### <a name="when-will-aci-be-in-a-specific-region"></a>ACI 何時可以在特定區域？

發行目前的區域可用性[此處](container-instances-region-availability.md#availability---general)，以及最新狀態資訊是否可透過[清單功能](/rest/api/container-instances/listcapabilities)API。 如果您有特定區域的需求，請連絡 Azure 支援。

## <a name="features-and-scenarios"></a>功能和案例

### <a name="how-do-i-scale-a-container-group"></a>如何調整容器群組？

目前，調整尚無法使用容器或容器群組。 如果您需要執行更多執行個體，使用我們的 API 來自動化，並建立更多容器群組建立服務的要求。 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>自訂的 VNet 中執行的執行個體可以使用哪些功能？

您可以部署您選擇的 Azure 虛擬網路中的容器群組，並委派到容器群組，以您的 Azure 資源之間路由傳送流量的 VNet 內的私人 Ip。 部署到虛擬網路部署的容器群組目前為預覽狀態，並公開上市 (GA) 之前可能會變更這項功能的某些層面。 請參閱[預覽限制](container-instances-vnet.md#preview-limitations)更新資訊。

## <a name="pricing"></a>價格

### <a name="when-does-the-meter-start-running"></a>計量何時開始執行？

容器群組持續時間的計算從我們一開始以提取您的第一個容器映像 （適用於新的部署），或您的容器群組重新啟動 （如果已部署），直到停止的容器群組的時間。 詳細資料，請參閱 <<c0> [ 容器執行個體定價](https://azure.microsoft.com/pricing/details/container-instances/)。

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>停止容器已停止時，收費？

計量會停止執行之後停止您整個容器的群組。 只要在容器群組中的容器正在執行，我們保留的資源，如果您想要再次啟動容器。 

## <a name="next-steps"></a>後續步驟

* [了解更多](container-instances-overview.md)關於 Azure 容器執行個體。
* [針對常見問題進行疑難排解](container-instances-troubleshooting.md)Azure Container Instances 中。