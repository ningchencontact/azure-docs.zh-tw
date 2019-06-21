---
title: Azure Service Fabric 版本
description: 最新的功能和增強功能，在 Service Fabric 中的版本資訊。
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 78fb96cae3d3d128da608183f37771b2ecf66dcf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165349"
---
# <a name="service-fabric-releases"></a>Service Fabric 版本

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">疑難排解指南</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">問題追蹤</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">支援選項</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">支援的版本</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">程式碼範例</a>

這篇文章提供詳細資訊的最新版本和 Service Fabric 執行階段和 Sdk 的更新。

## <a name="whats-new-in-service-fabric"></a>**在 Service Fabric 中最新消息**

### <a name="service-fabric-65"></a>Service Fabric 6.5

最新的 Service Fabric 版本包含可支援性、 可靠性和效能改進的新功能、 bug 修正和增強功能簡化叢集和應用程式生命週期管理。

> [!IMPORTANT]
> Service Fabric 6.5 是最終的版本與 Service Fabric 工具支援在 Visual Studio 2015。 移至建議客戶[Visual Studio 2019](https://visualstudio.microsoft.com/vs/)從現在開始。

以下是在 Service Fabric 6.5 中最新消息：

- 包含 Service Fabric Explorer[映像存放區的檢視器](service-fabric-visualizing-your-cluster.md#image-store-viewer)您已來檢查應用程式上傳映像存放區。

- [修補程式協調流程應用程式 (POA)](service-fabric-patch-orchestration-application.md)版本[1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0)包含許多自我診斷的改良。 建議的 POA 客戶移至這個版本。

- [預設會啟用 EventStore 服務](service-fabric-visualizing-your-cluster.md#event-store)適用於 Service Fabric 6.5 叢集，除非您已選擇退出。

- 新增[複本生命週期事件](service-fabric-diagnostics-event-generation-operational.md#replica-events)對於具狀態服務。

- [更好的可見性的種子節點狀態](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)，包括叢集層級警告，如果種子節點狀況不良 (*向下*，*已移除*或是*未知*)。

- [Service Fabric 應用程式的災害復原工具](https://github.com/Microsoft/Service-Fabric-AppDRTool)允許快速復原時主要叢集發生損毀的 Service Fabric 具狀態服務。 從主要的叢集中的資料會持續同步處理次要的待命資料庫應用程式，以使用定期備份和還原。

- Visual Studio 的支援，如[.NET Core 應用程式發行至以 Linux 為基礎的叢集](service-fabric-how-to-publish-linux-app-vs.md)。

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)將會自動安裝適用於 Service Fabric 6.5 （和更新版本） 升級或在 Azure 上建立新的 Linux 叢集。

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) MacOS/Linux OneBox 叢集上預設會安裝。

如需詳細資訊，請參閱 < [Service Fabric 6.5 版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)。

## <a name="previous-versions"></a>舊版

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2018 年 11 月 30日日 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [Azure Service Fabric 6.4 重新整理版本的 Windows 叢集](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019 年 2 月 4日日 | [Azure Service Fabric 6.4 重新整理版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019 年 3 月 4日日 | [Azure Service Fabric 6.4 重新整理版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019 年 4 月 8日日 | [Azure Service Fabric 6.4 重新整理版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019 5 月 2日日 | [Azure Service Fabric 6.4 重新整理版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 5 月 28日日 | [Azure Service Fabric 6.4 重新整理版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
