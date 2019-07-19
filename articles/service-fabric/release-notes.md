---
title: Azure Service Fabric 版本
description: Service Fabric 中的最新功能和改進的版本資訊。
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 829963cf13eb1dc1b1113b6a296fe77dadb63bc4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224943"
---
# <a name="service-fabric-releases"></a>Service Fabric 版本

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">疑難排解指南</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">問題追蹤</a><a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank"></a><a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank"></a><a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank"></a>支援選項支援的版本 
| 程式碼範例 
|   
| 

本文提供 Service Fabric 執行時間和 Sdk 最新版本和更新的詳細資訊。

## <a name="whats-new-in-service-fabric"></a>Service Fabric 的新功能

### <a name="service-fabric-65"></a>Service Fabric 6。5

最新的 Service Fabric 版本包括可支援性、可靠性和效能改良功能、新功能、bug 修正, 以及簡化叢集和應用程式生命週期管理的增強功能。

> [!IMPORTANT]
> Service Fabric 6.5 是 Visual Studio 2015 中具有 Service Fabric 工具支援的最終版本。 我們建議客戶繼續[Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 。

以下是 Service Fabric 6.5 的新這是什麼:

- Service Fabric Explorer 包含[映射存放區檢視器](service-fabric-visualizing-your-cluster.md#image-store-viewer), 可檢查您已上傳至映射存放區的應用程式。

- [修補程式協調流程應用程式 (POA)](service-fabric-patch-orchestration-application.md)版本[1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0)包含許多自我診斷改良功能。 建議將 POA 的客戶移至此版本。

- 除非您退出宣告, 否則 Service Fabric 6.5 叢集的[EventStore 服務預設為啟用](service-fabric-visualizing-your-cluster.md#event-store)。

- 已新增具狀態服務的[複本生命週期事件](service-fabric-diagnostics-event-generation-operational.md#replica-events)。

- [較佳的種子節點狀態可見度](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), 包括當種子節點狀況不良 (*關閉*、*移除*或*不明*) 時的叢集層級警告。

- [Service Fabric 應用程式嚴重損壞修復工具](https://github.com/Microsoft/Service-Fabric-AppDRTool)可讓 Service Fabric 具狀態服務在主要叢集發生嚴重損壞時快速復原。 主要叢集的資料會使用定期備份和還原, 持續在次要待命應用程式上進行同步處理。

- Visual Studio 支援[將 .Net Core 應用程式發佈到以 Linux 為基礎](service-fabric-how-to-publish-linux-app-vs.md)的叢集。

- 當您在 Azure 上升級或建立新的 Linux 叢集時, 將會自動安裝適用于 Service Fabric 6.5 (和更新版本) 的[azure SERVICE FABRIC CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) 。

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)預設會安裝在 MacOS/Linux OneBox 叢集上。

如需進一步的詳細資料, 請參閱[Service Fabric 6.5 版本](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)資訊。

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2019年6月11日 | [Azure Service Fabric 6。5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure Service Fabric 6.5 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [版本資訊](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |

## <a name="previous-versions"></a>舊版

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 版本

| 發行日期 | 版本 | 其他資訊 |
|---|---|---|
| 2018年11月30日 | [Azure Service Fabric 6。4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [適用于 Windows 叢集的 Azure Service Fabric 6.4 重新整理版本](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [版本資訊](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019年2月4日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019年3月4日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019年4月8日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019 5 月2日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 5 月28日 | [Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [版本資訊](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
