---
title: 啟用 Azure App Service 中的.NET 應用程式的快照集偵錯工具 |Microsoft Docs
description: 啟用 Azure App Service 中的.NET 應用程式的快照集偵錯工具
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 3e8ce3c2eff7b1f7184bb37f141e62563d4fe714
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612674"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>啟用 Azure App Service 中的.NET 應用程式的快照集偵錯工具

快照偵錯工具目前適用於 ASP.NET 和 ASP.NET Core 應用程式在 Azure App Service 上執行的 Windows 服務方案。

## <a id="installation"></a> 啟用快照集偵錯工具
若要啟用應用程式的快照集偵錯工具，請遵循下列指示。 如果您執行不同類型的 Azure 服務，以下是其他支援的平台上啟用快照集偵錯工具的指示：
* [Azure 雲端服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器和虛擬機器擴展集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [在內部部署虛擬或實體機器](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

如果您使用.NET Core 的 preview 版本，請遵循指示[啟用快照集偵錯工具的其他環境](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)第一次，以包含[Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)NuGet 封裝在應用程式，，，然後完成下列指示的其餘部分。 

Application Insights 快照集偵錯工具會預先安裝應用程式服務執行階段的一部分，但您需要將它開啟 App Service 應用程式取得快照集。 一旦您已部署應用程式，即使您在原始程式碼中包含 Application Insights SDK，請遵循下列步驟來啟用快照集偵錯工具。

1. 移至 Azure 入口網站中的 [App Service]  窗格。
2. 瀏覽至 [設定] > [Application Insights]  窗格。

   ![在 App Service 入口網站上啟用 Application Insights](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 依照窗格上的指示來建立新資源，或選取現有的 App Insights 資源來監視您的應用程式。 也請確定這兩個參數的快照集偵錯工具**上**。

   ![新增 App Insights 網站延伸模組][Enablement UI]

4. 快照偵錯工具現在是使用應用程式服務應用程式設定來啟用。

    ![快照偵錯工具的應用程式設定][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>停用快照集偵錯工具

遵循相同的步驟，針對**啟用快照集偵錯工具**，但快照集偵錯工具要切換這兩個參數**關閉**。
我們建議您擁有快照集偵錯工具在您的應用程式上啟用以簡化應用程式例外狀況的診斷。

## <a name="next-steps"></a>後續步驟

- 產生您可以觸發例外狀況的應用程式的流量。 然後，等待 10 到 15 分鐘會傳送至 Application Insights 執行個體的快照集。
- 請參閱[快照集](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)在 Azure 入口網站中。
- 快照偵錯工具問題進行疑難排解的協助，請參閱[快照集偵錯工具疑難排解](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

